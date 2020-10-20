---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 8fa45bf31cb5f1a7e35134f9513a40469719f8c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065616"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="070ec-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="070ec-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="070ec-104">EF Core 5.0 に予定されていたすべての機能が揃いました。</span><span class="sxs-lookup"><span data-stu-id="070ec-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="070ec-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="070ec-106">このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="070ec-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="070ec-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="070ec-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="070ec-108">RC1</span><span class="sxs-lookup"><span data-stu-id="070ec-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="070ec-109">多対多</span><span class="sxs-lookup"><span data-stu-id="070ec-109">Many-to-many</span></span>

<span data-ttu-id="070ec-110">結合テーブルを明示的にマッピングしなくても、EF Core 5.0 によって多対多のリレーションシップがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="070ec-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="070ec-111">たとえば、次のようなエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="070ec-111">For example, consider these entity types:</span></span>

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

<span data-ttu-id="070ec-112">`Post` に `Tags` のコレクションが含まれており、`Tag` に `Posts` のコレクションが含まれていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="070ec-113">EF Core 5.0 では、規則に従って多対多のリレーションシップとしてこれが認識されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="070ec-114">これは、`OnModelCreating` にコードが必要ないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="070ec-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="070ec-115">データベースの作成に移行 (または `EnsureCreated`) を使用すると、EF Core によって結合テーブルが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="070ec-116">たとえば、このモデルの SQL Server では、EF Core によって次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-116">For example, on SQL Server for this model, EF Core generates:</span></span>

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

<span data-ttu-id="070ec-117">`Blog` エンティティと `Post` エンティティを作成して関連付けると、結合テーブルの更新が自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="070ec-117">Creating and associating `Blog` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="070ec-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-118">For example:</span></span>

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

<span data-ttu-id="070ec-119">Post と Tag を挿入すると、EF によって結合テーブルに行が自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="070ec-120">たとえば、SQL Server の場合は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-120">For example, on SQL Server:</span></span>

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

<span data-ttu-id="070ec-121">クエリの場合、Include およびその他のクエリ操作は、他のリレーションシップと同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="070ec-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="070ec-122">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-122">For example:</span></span>

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

<span data-ttu-id="070ec-123">生成された SQL により、結合テーブルが自動的に使用されて、関連するすべての Tag が返されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

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

<span data-ttu-id="070ec-124">EF6 とは異なり、EF Core では結合テーブルの完全なカスタマイズが可能です。</span><span class="sxs-lookup"><span data-stu-id="070ec-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="070ec-125">たとえば、次のコードは、結合エンティティへのナビゲーションも持つ多対多のリレーションシップを構成します。結合エンティティにはペイロード プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

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

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="070ec-126">エンティティ型をクエリにマップする</span><span class="sxs-lookup"><span data-stu-id="070ec-126">Map entity types to queries</span></span>

<span data-ttu-id="070ec-127">エンティティ型は通常、その型に対してクエリを実行するときに、EF Core によってテーブルまたはビューの内容がプルバックされるようなテーブルまたはビューにマップされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-127">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="070ec-128">EF Core 5.0 では、エンティティ型を "クエリの定義" にマップできます</span><span class="sxs-lookup"><span data-stu-id="070ec-128">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="070ec-129">(これは以前のバージョンでは部分的にサポートされていましたが、大幅に改善され、EF Core 5.0 では構文が異なります)。</span><span class="sxs-lookup"><span data-stu-id="070ec-129">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="070ec-130">たとえば、2 つのテーブルについて考えてみます。1 つには最新の投稿が含まれ、もう 1 つには従来の投稿が含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-130">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="070ec-131">最新の投稿テーブルにはいくつかの列が追加されていますが、このアプリケーションの目的のために、最新の投稿と従来の投稿の両方を組み合わせて、必要なすべてのプロパティを持つエンティティ型にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-131">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

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

<span data-ttu-id="070ec-132">EF Core 5.0 では、`ToSqlQuery` を使用して、このエンティティ型を、両方のテーブルから行をプルして結合するクエリにマップできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-132">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="070ec-133">`legacy_posts` テーブルには `Category` 列がないことに注目してください。そのため、従来のすべての投稿に対して既定値を合成します。</span><span class="sxs-lookup"><span data-stu-id="070ec-133">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="070ec-134">こうすると、このエンティティ型が、LINQ クエリの通常の方法で使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-134">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="070ec-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-135">For example.</span></span> <span data-ttu-id="070ec-136">LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="070ec-136">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="070ec-137">SQLite で次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-137">Generates the following SQL on SQLite:</span></span>

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

<span data-ttu-id="070ec-138">エンティティ型用に構成されたクエリが、完全な LINQ クエリの作成の開始として使用されていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-138">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="070ec-139">イベント カウンター</span><span class="sxs-lookup"><span data-stu-id="070ec-139">Event counters</span></span>

<span data-ttu-id="070ec-140">[.NET イベント カウンター](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/)は、アプリケーションからパフォーマンス メトリックを効率的に公開するための手段です。</span><span class="sxs-lookup"><span data-stu-id="070ec-140">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="070ec-141">EF Core 5.0 には、`Microsoft.EntityFrameworkCore` カテゴリの下にイベント カウンターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-141">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="070ec-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-142">For example:</span></span>

```
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="070ec-143">これは、プロセス 49496 の EF Core イベントの収集を開始するように dotnet カウンターに指示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-143">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="070ec-144">これにより、コンソールに次のような出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-144">This generates output like this in the console:</span></span>

```
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

### <a name="property-bags"></a><span data-ttu-id="070ec-145">プロパティ バッグ</span><span class="sxs-lookup"><span data-stu-id="070ec-145">Property bags</span></span>

<span data-ttu-id="070ec-146">EF Core 5.0 を使用すると、同じ CLR 型を複数の異なるエンティティ型にマップできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-146">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="070ec-147">このような型は、共有型のエンティティ型と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="070ec-147">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="070ec-148">この機能と (Preview 1 に含まれていた) インデクサー プロパティを組み合わせることで、プロパティ バッグをエンティティ型として使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-148">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="070ec-149">たとえば、次の DbContext は、BCL 型 `Dictionary<string, object>` を製品とカテゴリの両方の共有型のエンティティ型として構成します。</span><span class="sxs-lookup"><span data-stu-id="070ec-149">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

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

<span data-ttu-id="070ec-150">ディクショナリ オブジェクト ("プロパティ バッグ") をエンティティ インスタンスとしてコンテキストに追加し、保存できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-150">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="070ec-151">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-151">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="070ec-152">これらのエンティティは、その後、通常の方法でクエリおよび更新できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-152">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="070ec-153">SaveChanges インターセプトとイベント</span><span class="sxs-lookup"><span data-stu-id="070ec-153">SaveChanges interception and events</span></span>

<span data-ttu-id="070ec-154">EF Core 5.0 には、SaveChanges が呼び出されたときにトリガーされる、.NET イベントと EF Core インターセプターの両方が導入されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-154">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="070ec-155">イベントの使用は簡単です。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-155">The events are simple to use; for example:</span></span>

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

<span data-ttu-id="070ec-156">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-156">Notice that:</span></span>
* <span data-ttu-id="070ec-157">イベントの送信元は `DbContext` インスタンス</span><span class="sxs-lookup"><span data-stu-id="070ec-157">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="070ec-158">`SavedChanges` イベントの引数には、データベースに保存されたエンティティの数が含まれている</span><span class="sxs-lookup"><span data-stu-id="070ec-158">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="070ec-159">インターセプターは `ISaveChangesInterceptor` で定義されますが、すべてのメソッドの実装を回避するために、`SaveChangesInterceptor` から継承すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="070ec-159">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convienient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="070ec-160">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-160">For example:</span></span>

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

<span data-ttu-id="070ec-161">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-161">Notice that:</span></span>
* <span data-ttu-id="070ec-162">インターセプターには同期と非同期の両方のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="070ec-162">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="070ec-163">これは、監査サーバーへの書き込みなど、非同期 I/O を実行する必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="070ec-163">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="070ec-164">インターセプターでは、すべてのインターセプターに共通の `InterceptionResult` メカニズムを使用して SaveChanges をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-164">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="070ec-165">インターセプターの欠点は、構築時に DbContext に登録する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="070ec-165">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="070ec-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-166">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="070ec-167">これに対し、イベントは、いつでも DbContext インスタンスに登録できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-167">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="070ec-168">移行からテーブルを除外する</span><span class="sxs-lookup"><span data-stu-id="070ec-168">Exclude tables from migrations</span></span>

<span data-ttu-id="070ec-169">1 つのエンティティ型を複数の DbContexts にマップすると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-169">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="070ec-170">これは、[境界付けられたコンテキスト](https://www.martinfowler.com/bliki/BoundedContext.html)を使用する場合に特に当てはまります。境界付けられたコンテキストでは、それぞれ異なる DbContext 型を持つことが一般的です。</span><span class="sxs-lookup"><span data-stu-id="070ec-170">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="070ec-171">たとえば、`User` 型は承認コンテキストとレポート コンテキストの両方で必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-171">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="070ec-172">`User` 型に変更が加えられると、両方の DbContexts の移行でデータベースの更新が試行されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-172">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="070ec-173">これを回避するには、いずれかのコンテキストのモデルを、その移行からテーブルを除外するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-173">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="070ec-174">次のコードでは、`AuthorizationContext` は `Users` テーブルに対する変更の移行を生成しますが、`ReportingContext` は生成しないため、移行の競合を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-174">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

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

### <a name="required-11-dependents"></a><span data-ttu-id="070ec-175">必要な一対一の依存</span><span class="sxs-lookup"><span data-stu-id="070ec-175">Required 1:1 dependents</span></span>

<span data-ttu-id="070ec-176">EF Core 3.1 では、一対一のリレーションシップの依存側は常に省略可能と見なされていました。</span><span class="sxs-lookup"><span data-stu-id="070ec-176">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="070ec-177">これは、所有エンティティを使用する場合に最も顕著でした。</span><span class="sxs-lookup"><span data-stu-id="070ec-177">This was most apparent when using owned entities.</span></span> <span data-ttu-id="070ec-178">たとえば、次のモデルと構成について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="070ec-178">For example, consider the following model and configuration:</span></span>

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

<span data-ttu-id="070ec-179">この結果、移行によって SQLite 用に次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-179">This results in Migrations creating the following table for SQLite:</span></span>

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

<span data-ttu-id="070ec-180">一部の `HomeAddress` プロパティは必要に応じて構成されていますが、すべての列が Null 許容であることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-180">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="070ec-181">また、`Person` に対してクエリを実行するときに、自宅または勤務先のアドレスのすべての列が null 値の場合、EF Core では、`HomeAddress` プロパティと `WorkAddress` プロパティの両方またはいずれかが null 値のままになり、`Address` の空のインスタンスは設定されません。</span><span class="sxs-lookup"><span data-stu-id="070ec-181">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="070ec-182">EF Core 5.0 では、`HomeAddress` ナビゲーションを必要な依存として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-182">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="070ec-183">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-183">For example:</span></span>

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

<span data-ttu-id="070ec-184">移行によって作成されたテーブルには、必要な依存の必須プロパティに null 非許容の列が含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-184">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

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

<span data-ttu-id="070ec-185">また、null 値の必要な依存を持つ所有者を保存しようとすると、EF Core によって例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-185">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="070ec-186">この例では、null 値の `HomeAddress` で `Person` を保存しようとすると、EF Core によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-186">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="070ec-187">必要な依存のすべての列に null 値が含まれている場合でも、最終的には、EF Core によって必要な依存のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-187">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="070ec-188">移行生成のオプション</span><span class="sxs-lookup"><span data-stu-id="070ec-188">Options for migration generation</span></span>

<span data-ttu-id="070ec-189">EF Core 5.0 によって、さまざまな目的のために移行の生成をより細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-189">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="070ec-190">メソッドによって実行できるタスクを次に示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-190">This includes the ability to:</span></span>

* <span data-ttu-id="070ec-191">スクリプトまたは即時実行に対して移行が生成されているかどうかを把握する</span><span class="sxs-lookup"><span data-stu-id="070ec-191">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="070ec-192">べき等スクリプトが生成されているかどうかを把握する</span><span class="sxs-lookup"><span data-stu-id="070ec-192">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="070ec-193">スクリプトでトランザクション ステートメントを除外する必要があるかどうかを把握する (以下の「_トランザクションを使用したスクリプトの移行_」を参照)</span><span class="sxs-lookup"><span data-stu-id="070ec-193">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="070ec-194">この動作は `MigrationsSqlGenerationOptions` 列挙型によって指定され、`IMigrator.GenerateScript` に渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-194">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="070ec-195">また、この作業には、必要に応じて SQL Server で `EXEC` を呼び出すことによる、より優れたべき等スクリプトの生成も含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-195">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="070ec-196">これにより、PostgreSQL など、他のデータベース プロバイダーによって生成されたスクリプトにも同様の機能強化が可能になります。</span><span class="sxs-lookup"><span data-stu-id="070ec-196">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="070ec-197">トランザクションを使用したスクリプトの移行</span><span class="sxs-lookup"><span data-stu-id="070ec-197">Migrations scripts with transactions</span></span>

<span data-ttu-id="070ec-198">移行から生成された SQL スクリプトには、移行に必要なトランザクションを開始およびコミットするステートメントが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-198">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="070ec-199">たとえば、次の移行スクリプトは 2 つの移行から生成されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-199">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="070ec-200">各移行がトランザクション内で適用されるようになったことに注目してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-200">Notice that each migration is now applied inside a transaction.</span></span>

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

<span data-ttu-id="070ec-201">前のセクションで説明したように、トランザクションを別の方法で処理する必要がある場合は、このトランザクションの使用を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-201">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="070ec-202">保留中の移行を表示する</span><span class="sxs-lookup"><span data-stu-id="070ec-202">See pending migrations</span></span>

<span data-ttu-id="070ec-203">この機能は、[@Psypher9](https://github.com/Psypher9) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-203">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="070ec-204">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-204">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-205">`dotnet ef migrations list` コマンドを使用すると、データベースにまだ適用されていない移行が表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-205">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="070ec-206">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-206">For example:</span></span>

```
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="070ec-207">さらに、同じ機能を備えたパッケージ マネージャー コンソール用の `Get-Migration` コマンドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-207">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="070ec-208">値の比較演算子用の ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="070ec-208">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="070ec-209">カスタムの可変型の EF Core プロパティには、プロパティの変更を正しく検出するために[値の比較演算子が必要](xref:core/modeling/value-comparers)です。</span><span class="sxs-lookup"><span data-stu-id="070ec-209">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="070ec-210">これは、型の値変換の構成の一部として指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-210">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="070ec-211">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-211">For example:</span></span>

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

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="070ec-212">EntityEntry TryGetValue メソッド</span><span class="sxs-lookup"><span data-stu-id="070ec-212">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="070ec-213">この機能は、[@m4ss1m0g](https://github.com/m4ss1m0g) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-213">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="070ec-214">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-214">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-215">`TryGetValue` メソッドが `EntityEntry.CurrentValues` と `EntityEntry.OriginalValues` に追加されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-215">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="070ec-216">これにより、プロパティが EF モデルでマップされているかどうかを最初に確認することなく、プロパティの値を要求することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-216">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="070ec-217">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-217">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="070ec-218">SQL Server の既定の最大バッチ サイズ</span><span class="sxs-lookup"><span data-stu-id="070ec-218">Default max batch size for SQL Server</span></span>

<span data-ttu-id="070ec-219">EF Core 5.0 から、SQL Server の SaveChanges の既定の最大バッチ サイズが 42 になりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-219">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="070ec-220">よく知られているように、これは生命、宇宙、そして万物についての究極の疑問の答えでもあります。</span><span class="sxs-lookup"><span data-stu-id="070ec-220">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="070ec-221">しかし、この値は、[バッチ処理のパフォーマンスの分析](https://github.com/dotnet/efcore/issues/9270)を通じて得られたものであるため、おそらく偶然でしょう。</span><span class="sxs-lookup"><span data-stu-id="070ec-221">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="070ec-222">究極の疑問の形式を発見したとは思っていませんが、SQL Server がなぜそのように機能するのかを理解するために地球が作られたというのは、多少もっともらしく思われます。</span><span class="sxs-lookup"><span data-stu-id="070ec-222">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="070ec-223">開発の既定の環境</span><span class="sxs-lookup"><span data-stu-id="070ec-223">Default environment to Development</span></span>

<span data-ttu-id="070ec-224">EF Core コマンド ライン ツールによって、環境変数 `ASPNETCORE_ENVIRONMENT` "_および_" `DOTNET_ENVIRONMENT` が "Development" に自動的に構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-224">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="070ec-225">これにより、汎用ホストを使用する場合のエクスペリエンスと、開発時の ASP.NET Core のエクスペリエンスが一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-225">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="070ec-226">[#19903](https://github.com/dotnet/efcore/issues/19903) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-226">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="070ec-227">移行列の順序付けの改善</span><span class="sxs-lookup"><span data-stu-id="070ec-227">Better migrations column ordering</span></span>

<span data-ttu-id="070ec-228">マップされていない基底クラスの列が、マップされたエンティティ型の他の列の後に並べ替えられるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-228">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="070ec-229">これは、新しく作成されたテーブルにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="070ec-229">Note this only impacts newly created tables.</span></span> <span data-ttu-id="070ec-230">既存のテーブルの列の順序は変更されません。</span><span class="sxs-lookup"><span data-stu-id="070ec-230">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="070ec-231">[#11314](https://github.com/dotnet/efcore/issues/11314) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-231">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="070ec-232">クエリの機能強化</span><span class="sxs-lookup"><span data-stu-id="070ec-232">Query improvements</span></span>

<span data-ttu-id="070ec-233">EF Core 5.0 RC1 には、クエリ変換の機能強化がいくつか追加されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-233">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="070ec-234">Cosmos での `is` の翻訳-- [#16391](https://github.com/dotnet/efcore/issues/16391) を参照</span><span class="sxs-lookup"><span data-stu-id="070ec-234">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="070ec-235">null 値の反映を制御するためにユーザーマップ関数に注釈を付けられるようになりました-- [#19609](https://github.com/dotnet/efcore/issues/19609) を参照</span><span class="sxs-lookup"><span data-stu-id="070ec-235">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="070ec-236">条件付き集計による GroupBy の翻訳のサポート-- [#11711](https://github.com/dotnet/efcore/issues/11711) を参照</span><span class="sxs-lookup"><span data-stu-id="070ec-236">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="070ec-237">集計前の group 要素に対する Distinct 演算子の翻訳-- [#17376](https://github.com/dotnet/efcore/issues/17376) を参照</span><span class="sxs-lookup"><span data-stu-id="070ec-237">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="070ec-238">フィールドのモデル構築</span><span class="sxs-lookup"><span data-stu-id="070ec-238">Model building for fields</span></span>

<span data-ttu-id="070ec-239">最後に RC1 で、EF Core により ModelBuilder でフィールドとプロパティに対して、ラムダ メソッドを使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-239">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="070ec-240">たとえば、あなたが何らかの理由でプロパティを嫌っていて、パブリック フィールドを使用することにした場合、これらのフィールドをラムダ ビルダーを使用してマッピングできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-240">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

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

<span data-ttu-id="070ec-241">可能になったとはいえ、これを行うことは推奨されていません。</span><span class="sxs-lookup"><span data-stu-id="070ec-241">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="070ec-242">また、これによって EF Core にフィールド マッピング機能が追加されることはなく、常に文字列メソッドを必要とする代わりにラムダ メソッドを使用できるようになるだけだということにご注意ください。</span><span class="sxs-lookup"><span data-stu-id="070ec-242">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="070ec-243">フィールドが公開されることはめったにないので、これはあまり役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="070ec-243">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="070ec-244">Preview 8</span><span class="sxs-lookup"><span data-stu-id="070ec-244">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="070ec-245">Table-Per-Type (TPT) のマッピング</span><span class="sxs-lookup"><span data-stu-id="070ec-245">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="070ec-246">EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-246">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="070ec-247">これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="070ec-247">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="070ec-248">EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="070ec-248">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="070ec-249">たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="070ec-249">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="070ec-250">EF Core の既定では、これが 1 つのテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-250">By default, EF Core will map this to a single table:</span></span>

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

<span data-ttu-id="070ec-251">一方、各エンティティ型を別のテーブルにマップすると、代わりに型ごとに 1 つのテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-251">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

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

<span data-ttu-id="070ec-252">前述の外部キー制約の作成は、プレビュー 8 のコードを分岐した後に追加されたことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-252">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="070ec-253">エンティティ型を別のテーブルにマップするには、マッピング属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="070ec-253">Entity types can be mapped to different tables using mapping attributes:</span></span>

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

<span data-ttu-id="070ec-254">または `ModelBuilder` 構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="070ec-254">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="070ec-255">ドキュメントは、イシュー [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-255">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="070ec-256">移行:SQLite テーブルを再構築する</span><span class="sxs-lookup"><span data-stu-id="070ec-256">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="070ec-257">他のデータベースと比較すると、SQLite のスキーマ操作機能は比較的限られています。</span><span class="sxs-lookup"><span data-stu-id="070ec-257">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="070ec-258">たとえば、既存のテーブルから列を削除するには、テーブル全体を削除して再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-258">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="070ec-259">EF Core 5.0 の移行では、必要なスキーマ変更のためにテーブルの自動再構築がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-259">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="070ec-260">たとえば、エンティティ型 `Unicorn` に対して作成された `Unicorns` テーブルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="070ec-260">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

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

<span data-ttu-id="070ec-261">次に、ユニコーンの年齢を格納することは非常に失礼だと考えられることがわかったので、そのプロパティを削除し、新しい移行を追加して、データベースを更新しましょう。</span><span class="sxs-lookup"><span data-stu-id="070ec-261">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="070ec-262">EF Core 3.1 を使用している場合は列を削除できないため、この更新は失敗します。</span><span class="sxs-lookup"><span data-stu-id="070ec-262">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="070ec-263">EF Core 5.0 ではそうではなく、移行によってテーブルが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-263">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

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

<span data-ttu-id="070ec-264">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-264">Notice that:</span></span>
* <span data-ttu-id="070ec-265">新しいテーブルに必要なスキーマを使用して一時テーブルが作成されます</span><span class="sxs-lookup"><span data-stu-id="070ec-265">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="070ec-266">データは現在のテーブルから一時テーブルにコピーされます</span><span class="sxs-lookup"><span data-stu-id="070ec-266">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="070ec-267">外部キーの適用はオフに切り替えられます</span><span class="sxs-lookup"><span data-stu-id="070ec-267">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="070ec-268">現在のテーブルはドロップされます</span><span class="sxs-lookup"><span data-stu-id="070ec-268">The current table is dropped</span></span>
* <span data-ttu-id="070ec-269">一時テーブルは新しいテーブルとして名前が変更されます</span><span class="sxs-lookup"><span data-stu-id="070ec-269">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="070ec-270">ドキュメントは、イシュー [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-270">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="070ec-271">テーブル値関数</span><span class="sxs-lookup"><span data-stu-id="070ec-271">Table-valued functions</span></span>

<span data-ttu-id="070ec-272">この機能は、[@pmiddleton](https://github.com/pmiddleton) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-272">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="070ec-273">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-273">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-274">EF Core 5.0 には、.NET メソッドをテーブル値関数 (TVF) にマップするための最上級のサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-274">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="070ec-275">これらの関数を LINQ クエリで使用することができます。また、この関数の結果に対する追加の構成も SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-275">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="070ec-276">たとえば、SQL Server データベースで定義されている次の TVF について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="070ec-276">For example, consider this TVF defined in a SQL Server database:</span></span>

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

<span data-ttu-id="070ec-277">EF Core モデルでは、この TVF を使用するために 2 つのエンティティ型が必要です。</span><span class="sxs-lookup"><span data-stu-id="070ec-277">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="070ec-278">通常の方法で Employees テーブルにマップする `Employee` 型</span><span class="sxs-lookup"><span data-stu-id="070ec-278">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="070ec-279">TVF から返されるシェイプと一致する `Report` 型</span><span class="sxs-lookup"><span data-stu-id="070ec-279">A `Report` type that matches the shape returned by the TVF</span></span>

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

<span data-ttu-id="070ec-280">これらの型は、EF Core モデルに含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-280">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="070ec-281">`Report` には主キーがないため、そのように構成する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-281">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="070ec-282">最後に、.NET メソッドをデータベースの TVF にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-282">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="070ec-283">このメソッドは、新しい `FromExpression` メソッドを使用して DbContext に対して定義できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-283">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="070ec-284">このメソッドには、上記で定義された TVF に一致するパラメーターと戻り値の型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-284">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="070ec-285">次に、メソッドは OnModelCreating の EF Core モデルに追加されます</span><span class="sxs-lookup"><span data-stu-id="070ec-285">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="070ec-286">(ここでラムダを使用すると、`MethodInfo` を EF Core に簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-286">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="070ec-287">メソッドに渡された引数は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="070ec-287">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="070ec-288">これで、`GetReports` を呼び出して結果を構成するクエリを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-288">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="070ec-289">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-289">For example:</span></span>

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

<span data-ttu-id="070ec-290">SQL Server では、これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-290">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="070ec-291">SQL では `Employees` テーブルがルートとされ、`GetReports` が呼び出され、関数の結果に WHERE 句が追加されることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-291">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="070ec-292">柔軟なクエリと更新マップ</span><span class="sxs-lookup"><span data-stu-id="070ec-292">Flexible query/update mapping</span></span>

<span data-ttu-id="070ec-293">EF Core 5.0 では、同じエンティティ型を異なるデータベース オブジェクトにマップできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-293">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="070ec-294">これらのオブジェクトは、テーブル、ビュー、または関数です。</span><span class="sxs-lookup"><span data-stu-id="070ec-294">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="070ec-295">たとえば、エンティティ型はデータベース ビューとデータベース テーブルの両方にマップできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-295">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="070ec-296">既定では、EF Core はビューからクエリを実行し、更新をテーブルに送信します。</span><span class="sxs-lookup"><span data-stu-id="070ec-296">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="070ec-297">たとえば、次のコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="070ec-297">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="070ec-298">ビューに対してクエリを実行し、テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="070ec-298">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="070ec-299">コンテキスト全体の分割クエリ構成</span><span class="sxs-lookup"><span data-stu-id="070ec-299">Context-wide split-query configuration</span></span>

<span data-ttu-id="070ec-300">分割クエリ (以下を参照してください) は、DbContext によって実行されるクエリの既定値として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-300">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="070ec-301">この構成はリレーショナル プロバイダーの場合にのみ使用できるため、`UseProvider` 構成の一部として指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-301">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="070ec-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-302">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="070ec-303">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-303">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="070ec-304">PhysicalAddress のマッピング</span><span class="sxs-lookup"><span data-stu-id="070ec-304">PhysicalAddress mapping</span></span>

<span data-ttu-id="070ec-305">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-305">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="070ec-306">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-306">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-307">この構成はリレーショナル プロバイダーでのみ使用できるた標準の .NET [PhysicalAddress クラス](/dotnet/api/system.net.networkinformation.physicaladdress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-307">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="070ec-308">詳細については、以下の `IPAddress` の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-308">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="070ec-309">Preview 7</span><span class="sxs-lookup"><span data-stu-id="070ec-309">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="070ec-310">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="070ec-310">DbContextFactory</span></span>

<span data-ttu-id="070ec-311">EF Core 5.0 には、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-311">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="070ec-312">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-312">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="070ec-313">これにより、ASP.NET Core コントローラーなどのアプリケーション サービスが、サービス コンストラクターの `IDbContextFactory<TContext>` に依存できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-313">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="070ec-314">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-314">For example:</span></span>

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

<span data-ttu-id="070ec-315">これにより、DbContext インスタンスを必要に応じて作成および使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-315">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="070ec-316">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-316">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="070ec-317">なお、この方法で作成された DbContext インスタンスは、アプリケーションのサービス プロバイダーには管理 "_されず_"、従ってアプリケーションによって破棄される必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-317">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="070ec-318">この分離は、`IDbContextFactory` の使用が推奨される Blazor アプリケーションで非常に便利ですが、他のシナリオでも役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-318">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="070ec-319">DbContext インスタンスは、`AddPooledDbContextFactory` を呼び出すことによってプールできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-319">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="070ec-320">このプールは、`AddDbContextPool` の場合と同じように動作しますが、同じ制限もあります。</span><span class="sxs-lookup"><span data-stu-id="070ec-320">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="070ec-321">ドキュメントは、イシュー [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-321">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="070ec-322">DbContext の状態のリセット</span><span class="sxs-lookup"><span data-stu-id="070ec-322">Reset DbContext state</span></span>

<span data-ttu-id="070ec-323">EF Core 5.0 には、追跡対象のすべてのエンティティの DbContext をクリアする `ChangeTracker.Clear()` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-323">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="070ec-324">これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。</span><span class="sxs-lookup"><span data-stu-id="070ec-324">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="070ec-325">ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、パフォーマンスと堅牢性が向上します。</span><span class="sxs-lookup"><span data-stu-id="070ec-325">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="070ec-326">ドキュメントは、イシュー [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-326">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="070ec-327">ストアで生成された既定値に新しいパターン</span><span class="sxs-lookup"><span data-stu-id="070ec-327">New pattern for store-generated defaults</span></span>

<span data-ttu-id="070ec-328">EF Core では、既定の制約値がある列に、値を明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-328">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="070ec-329">EF Core では、このセンチネルとして、プロパティ型に CLR の既定値を使用しています。つまり、その値が CLR の既定でない場合はそれが挿入され、それ以外の場合は、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-329">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="070ec-330">これは、既定の CLR が適切なセンチネルではない型 (特に、`bool` のプロパティ) で問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="070ec-330">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="070ec-331">EF Core 5.0 で、次のようなケースで、バッキング フィールドに null 値が許容されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-331">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="070ec-332">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-332">For example:</span></span>

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

<span data-ttu-id="070ec-333">なお、null 値はバッキング フィールドでは許容されますが、公開されているプロパティでは許容されません。</span><span class="sxs-lookup"><span data-stu-id="070ec-333">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="070ec-334">これにより、エンティティ型の公開サーフェスに影響を与えることなく、センチネル値を `null` にできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-334">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="070ec-335">この場合、`IsValid` が設定されない場合、バッキング フィールドは null のままであるため、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-335">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="070ec-336">`true` または `false` のいずれかが設定される場合、この値はデータベースに明示的に保存されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-336">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="070ec-337">ドキュメントは、イシュー [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-337">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="070ec-338">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="070ec-338">Cosmos partition keys</span></span>

<span data-ttu-id="070ec-339">EF Core では、EF モデルに Cosmos パーティション キーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-339">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="070ec-340">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-340">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="070ec-341">Preview 7 以降では、一部のクエリのパフォーマンスの向上のために、パーティション キーがエンティティ型の PK に含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-341">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="070ec-342">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-342">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="070ec-343">Cosmos の構成</span><span class="sxs-lookup"><span data-stu-id="070ec-343">Cosmos configuration</span></span>

<span data-ttu-id="070ec-344">EF Core 5.0 では、Cosmos と Cosmos への接続の構成が改善されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-344">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="070ec-345">以前は、EF Core を Cosmos データベースに接続する場合に、エンドポイントとキーを明示的に指定する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="070ec-345">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="070ec-346">EF Core 5.0 では、代わりに接続文字列を使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-346">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="070ec-347">また、EF Core 5.0 では、WebProxy インスタンスを明示的に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-347">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="070ec-348">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-348">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="070ec-349">その他、多くのタイムアウト値、制限なども構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-349">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="070ec-350">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-350">For example:</span></span>

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

<span data-ttu-id="070ec-351">最後に、一般により互換性がある、`ConnectionMode.Gateway` が既定の接続モードになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-351">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="070ec-352">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-352">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="070ec-353">DbContext のスキャフォールディングの単数化</span><span class="sxs-lookup"><span data-stu-id="070ec-353">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="070ec-354">以前は EF Core で、既存のデータベースから DbContext をスキャフォールディングする場合、データベース内のテーブル名と一致するエンティティ型名が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="070ec-354">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="070ec-355">たとえば、`People` テーブルと `Addresses` テーブルには、`People` と `Addresses` という名前のエンティティ型が生成されていました。</span><span class="sxs-lookup"><span data-stu-id="070ec-355">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="070ec-356">この動作は、以前のリリースでは、複数形化サービスの登録によって構成できました。</span><span class="sxs-lookup"><span data-stu-id="070ec-356">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="070ec-357">EF Core 5.0 では、既定の複数形化サービスとして [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) パッケージが使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-357">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="070ec-358">つまり、`People` テーブルと `Addresses` テーブルは、`Person` と `Address` という名前のエンティティ型にリバース エンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-358">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="070ec-359">セーブポイント</span><span class="sxs-lookup"><span data-stu-id="070ec-359">Savepoints</span></span>

<span data-ttu-id="070ec-360">EF Core では、複数の操作を実行するトランザクションをより細かく制御する、[セーブポイント](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-360">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="070ec-361">セーブポイントは、手動で作成、解放、ロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-361">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="070ec-362">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-362">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="070ec-363">また、`SaveChanges` の実行に失敗すると、EF Core では最後のセーブポイントにロールバックされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-363">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="070ec-364">これにより、トランザクション全体を再実行しなくても、SaveChanges を再実行できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-364">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="070ec-365">ドキュメントは、イシュー [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-365">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="070ec-366">Preview 6</span><span class="sxs-lookup"><span data-stu-id="070ec-366">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="070ec-367">関連するコレクションのクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="070ec-367">Split queries for related collections</span></span>

<span data-ttu-id="070ec-368">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-368">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="070ec-369">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-369">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="070ec-370">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-370">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="070ec-371">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-371">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="070ec-372">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-372">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="070ec-373">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-373">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="070ec-374">インクルードを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="070ec-374">Split queries with Include</span></span>

<span data-ttu-id="070ec-375">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="070ec-375">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="070ec-376">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-376">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="070ec-377">新しい `AsSplitQuery` API を使用して、この動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-377">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="070ec-378">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-378">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="070ec-379">AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-379">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="070ec-380">EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-380">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="070ec-381">クエリ ルートに対するすべての操作がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="070ec-381">All operations on the query root are supported.</span></span> <span data-ttu-id="070ec-382">これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="070ec-382">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="070ec-383">OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。</span><span class="sxs-lookup"><span data-stu-id="070ec-383">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="070ec-384">コレクション プロジェクションを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="070ec-384">Split queries with collection projections</span></span>

<span data-ttu-id="070ec-385">`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-385">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="070ec-386">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-386">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="070ec-387">この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-387">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="070ec-388">サポートされるのはコレクションの具体化のみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-388">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="070ec-389">上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。</span><span class="sxs-lookup"><span data-stu-id="070ec-389">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="070ec-390">この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-390">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="070ec-391">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="070ec-391">IndexAttribute</span></span>

<span data-ttu-id="070ec-392">新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-392">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="070ec-393">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-393">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="070ec-394">SQL Server の場合、移行によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-394">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="070ec-395">IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-395">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="070ec-396">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-396">For example:</span></span>

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

<span data-ttu-id="070ec-397">SQL Server の場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-397">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="070ec-398">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-398">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="070ec-399">クエリ変換の例外の改善</span><span class="sxs-lookup"><span data-stu-id="070ec-399">Improved query translation exceptions</span></span>

<span data-ttu-id="070ec-400">クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。</span><span class="sxs-lookup"><span data-stu-id="070ec-400">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="070ec-401">たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。</span><span class="sxs-lookup"><span data-stu-id="070ec-401">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="070ec-402">EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="070ec-402">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="070ec-403">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="070ec-403">Unhandled exception.</span></span> <span data-ttu-id="070ec-404">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.IsSigned)' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="070ec-404">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="070ec-405">追加情報:エンティティ型 'Artist' のメンバー 'IsSigned' の変換に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="070ec-405">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="070ec-406">指定されたメンバーがマップされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-406">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="070ec-407">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="070ec-407">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="070ec-408">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-408">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="070ec-409">同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-409">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="070ec-410">たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="070ec-410">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="070ec-411">EF Core では、次の例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-411">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="070ec-412">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="070ec-412">Unhandled exception.</span></span> <span data-ttu-id="070ec-413">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="070ec-413">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="070ec-414">追加情報:'StringComparison' 引数を受け取る 'string.Equals' メソッドの変換はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="070ec-414">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="070ec-415">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2129535 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-415">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="070ec-416">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="070ec-416">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="070ec-417">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-417">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="070ec-418">トランザクション ID を指定する</span><span class="sxs-lookup"><span data-stu-id="070ec-418">Specify transaction ID</span></span>

<span data-ttu-id="070ec-419">この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-419">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="070ec-420">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-420">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-421">EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-421">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="070ec-422">この ID は通常、トランザクションの開始時に EF Core によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-422">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="070ec-423">アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-423">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="070ec-424">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-424">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="070ec-425">IPAddress マッピング</span><span class="sxs-lookup"><span data-stu-id="070ec-425">IPAddress mapping</span></span>

<span data-ttu-id="070ec-426">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-426">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="070ec-427">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-427">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-428">標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-428">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="070ec-429">たとえば、次のエンティティ型をマップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-429">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="070ec-430">SQL Server では、移行によって次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-430">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="070ec-431">これで、通常の方法でエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-431">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="070ec-432">結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-432">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="070ec-433">スキャフォールディング時の OnConfiguring を除外する</span><span class="sxs-lookup"><span data-stu-id="070ec-433">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="070ec-434">DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-434">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="070ec-435">ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="070ec-435">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="070ec-436">これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-436">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="070ec-437">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-437">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="070ec-438">または、パッケージ マネージャー コンソールで:</span><span class="sxs-lookup"><span data-stu-id="070ec-438">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="070ec-439">[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="070ec-439">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="070ec-440">文字列の FirstOrDefault の変換</span><span class="sxs-lookup"><span data-stu-id="070ec-440">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="070ec-441">この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-441">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="070ec-442">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="070ec-442">Many thanks for the contribution!</span></span>

<span data-ttu-id="070ec-443">FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-443">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="070ec-444">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="070ec-444">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="070ec-445">は、SQL Server を使用すると、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-445">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="070ec-446">CASE ブロックの簡略化</span><span class="sxs-lookup"><span data-stu-id="070ec-446">Simplify case blocks</span></span>

<span data-ttu-id="070ec-447">EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-447">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="070ec-448">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="070ec-448">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="070ec-449">は、SQL Server では、正式には次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="070ec-449">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="070ec-450">しかし、今後は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-450">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="070ec-451">Preview 5</span><span class="sxs-lookup"><span data-stu-id="070ec-451">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="070ec-452">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="070ec-452">Database collations</span></span>

<span data-ttu-id="070ec-453">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-453">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="070ec-454">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="070ec-454">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="070ec-455">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-455">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="070ec-456">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-456">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="070ec-457">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-457">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="070ec-458">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-458">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="070ec-459">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-459">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="070ec-460">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="070ec-460">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="070ec-461">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-461">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="070ec-462">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-462">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="070ec-463">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-463">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="070ec-464">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-464">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="070ec-465">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="070ec-465">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="070ec-466">引数は、コマンド ラインから [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-466">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="070ec-467">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-467">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="070ec-468">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-468">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="070ec-469">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-469">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="070ec-470">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-470">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="070ec-471">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="070ec-471">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="070ec-472">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-472">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="070ec-473">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-473">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="070ec-474">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-474">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="070ec-475">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="070ec-475">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="070ec-476">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-476">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="070ec-477">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-477">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="070ec-478">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-478">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="070ec-479">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="070ec-479">Stored (persisted) computed columns</span></span>

<span data-ttu-id="070ec-480">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-480">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="070ec-481">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="070ec-481">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="070ec-482">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-482">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="070ec-483">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-483">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="070ec-484">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-484">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="070ec-485">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="070ec-485">SQLite computed columns</span></span>

<span data-ttu-id="070ec-486">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-486">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="070ec-487">Preview 4</span><span class="sxs-lookup"><span data-stu-id="070ec-487">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="070ec-488">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="070ec-488">Configure database precision/scale in model</span></span>

<span data-ttu-id="070ec-489">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-489">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="070ec-490">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-490">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="070ec-491">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-491">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="070ec-492">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-492">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="070ec-493">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="070ec-493">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="070ec-494">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-494">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="070ec-495">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-495">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="070ec-496">Preview 3</span><span class="sxs-lookup"><span data-stu-id="070ec-496">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="070ec-497">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="070ec-497">Filtered Include</span></span>

<span data-ttu-id="070ec-498">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-498">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="070ec-499">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-499">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="070ec-500">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-500">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="070ec-501">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-501">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="070ec-502">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-502">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="070ec-503">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-503">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="070ec-504">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-504">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="070ec-505">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="070ec-505">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="070ec-506">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-506">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="070ec-507">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-507">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="070ec-508">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="070ec-508">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="070ec-509">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-509">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="070ec-510">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-510">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="070ec-511">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-511">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="070ec-512">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="070ec-512">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="070ec-513">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-513">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="070ec-514">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="070ec-514">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="070ec-515">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-515">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="070ec-516">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-516">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="070ec-517">詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070ec-517">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="070ec-518">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-518">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="070ec-519">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="070ec-519">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="070ec-520">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="070ec-520">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="070ec-521">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="070ec-521">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="070ec-522">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-522">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="070ec-523">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-523">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="070ec-524">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-524">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="070ec-525">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="070ec-525">Cosmos partition keys</span></span>

<span data-ttu-id="070ec-526">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-526">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="070ec-527">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-527">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="070ec-528">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-528">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="070ec-529">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="070ec-529">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="070ec-530">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-530">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="070ec-531">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-531">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="070ec-532">Preview 2</span><span class="sxs-lookup"><span data-stu-id="070ec-532">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="070ec-533">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="070ec-533">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="070ec-534">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-534">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="070ec-535">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="070ec-535">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="070ec-536">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-536">For example:</span></span>

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

<span data-ttu-id="070ec-537">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-537">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="070ec-538">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="070ec-538">Complete discriminator mapping</span></span>

<span data-ttu-id="070ec-539">EF Core では、[継承階層の TPH マッピング](xref:core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-539">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="070ec-540">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-540">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="070ec-541">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-541">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="070ec-542">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-542">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="070ec-543">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-543">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="070ec-544">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="070ec-544">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="070ec-545">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="070ec-545">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="070ec-546">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-546">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="070ec-547">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-547">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="070ec-548">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-548">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="070ec-549">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-549">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="070ec-550">Preview 1</span><span class="sxs-lookup"><span data-stu-id="070ec-550">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="070ec-551">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="070ec-551">Simple logging</span></span>

<span data-ttu-id="070ec-552">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-552">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="070ec-553">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-553">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="070ec-554">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-554">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="070ec-555">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-555">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="070ec-556">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="070ec-556">Simple way to get generated SQL</span></span>

<span data-ttu-id="070ec-557">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="070ec-557">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="070ec-558">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-558">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="070ec-559">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-559">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="070ec-560">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="070ec-560">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="070ec-561">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-561">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="070ec-562">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-562">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="070ec-563">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-563">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="070ec-564">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="070ec-564">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="070ec-565">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-565">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="070ec-566">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-566">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="070ec-567">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="070ec-567">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="070ec-568">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-568">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="070ec-569">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="070ec-569">Change-tracking proxies</span></span>

<span data-ttu-id="070ec-570">EF Core で、[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-570">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="070ec-571">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="070ec-571">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="070ec-572">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="070ec-572">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="070ec-573">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-573">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="070ec-574">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="070ec-574">Enhanced debug views</span></span>

<span data-ttu-id="070ec-575">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-575">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="070ec-576">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-576">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="070ec-577">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="070ec-577">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="070ec-578">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-578">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="070ec-579">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-579">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="070ec-580">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="070ec-580">Improved handling of database null semantics</span></span>

<span data-ttu-id="070ec-581">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="070ec-581">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="070ec-582">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="070ec-582">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="070ec-583">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="070ec-583">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="070ec-584">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="070ec-584">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="070ec-585">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-585">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="070ec-586">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="070ec-586">Indexer properties</span></span>

<span data-ttu-id="070ec-587">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="070ec-587">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="070ec-588">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="070ec-588">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="070ec-589">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-589">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="070ec-590">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="070ec-590">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="070ec-591">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-591">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="070ec-592">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-592">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="070ec-593">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-593">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="070ec-594">IsRelational</span><span class="sxs-lookup"><span data-stu-id="070ec-594">IsRelational</span></span>

<span data-ttu-id="070ec-595">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="070ec-595">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="070ec-596">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="070ec-596">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="070ec-597">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-597">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="070ec-598">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-598">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="070ec-599">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="070ec-599">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="070ec-600">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-600">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="070ec-601">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="070ec-601">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="070ec-602">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](xref:core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="070ec-602">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="070ec-603">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-603">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="070ec-604">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="070ec-604">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="070ec-605">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-605">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="070ec-606">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="070ec-606">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="070ec-607">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="070ec-607">DateDiffWeek</span></span>
* <span data-ttu-id="070ec-608">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="070ec-608">DateFromParts</span></span>

<span data-ttu-id="070ec-609">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-609">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="070ec-610">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-610">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="070ec-611">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="070ec-611">Query translations for more byte array constructs</span></span>

<span data-ttu-id="070ec-612">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-612">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="070ec-613">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="070ec-613">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="070ec-614">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-614">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="070ec-615">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="070ec-615">Query translation for Reverse</span></span>

<span data-ttu-id="070ec-616">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-616">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="070ec-617">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="070ec-617">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="070ec-618">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-618">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="070ec-619">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="070ec-619">Query translation for bitwise operators</span></span>

<span data-ttu-id="070ec-620">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-620">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="070ec-621">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-621">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="070ec-622">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="070ec-622">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="070ec-623">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="070ec-623">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="070ec-624">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="070ec-624">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

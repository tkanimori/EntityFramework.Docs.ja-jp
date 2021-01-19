---
title: 連鎖削除 - EF Core
description: エンティティがプリンシパルまたは親から切断されたときにトリガーされるカスケード動作の構成
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 7c35de900930cf42da0e534df76124b5fb19ca52
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128863"
---
# <a name="cascade-delete"></a><span data-ttu-id="cef2f-103">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="cef2f-103">Cascade Delete</span></span>

<span data-ttu-id="cef2f-104">Entity Framework Core (EF Core) において、リレーションシップは外部キーを使用して表されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="cef2f-105">外部キーを含むエンティティは、リレーションシップの子または依存エンティティです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="cef2f-106">このエンティティの外部キー値は、関連するプリンシパルまたは親エンティティの主キー値 (または代替キー値) と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="cef2f-107">プリンシパルまたは親エンティティが削除された場合、依存元または子の外部キー値は、"_どの_" プリンシパルまたは親の主あるいは代替キーとも一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="cef2f-108">これは無効な状態であり、ほとんどのデータベースで参照制約違反が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="cef2f-109">この参照に関する制約違反を回避するには、次の 2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="cef2f-110">FK 値を null 値に設定する</span><span class="sxs-lookup"><span data-stu-id="cef2f-110">Set the FK values to null</span></span>
2. <span data-ttu-id="cef2f-111">依存または子エンティティも削除する</span><span class="sxs-lookup"><span data-stu-id="cef2f-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="cef2f-112">最初のオプションは、省略可能なリレーションシップに対してのみ有効であり、かつ外部キー プロパティ (およびマップ先のデータベース列) が Null 許容でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="cef2f-113">2 番目のオプションは、任意の種類のリレーションシップに対して有効であり、"カスケード削除" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="cef2f-114">このドキュメントでは、データベースの更新の観点から、カスケード削除 (および孤立の削除) について説明します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="cef2f-115">それと密接に関連する概念が「[EF Core での変更の追跡](xref:core/change-tracking/index)」および「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」で説明されています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="cef2f-116">これらの概念を十分に理解してから、ここに示す内容を読み進めてください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="cef2f-117">このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes)することで実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="cef2f-118">カスケード動作が発生するタイミング</span><span class="sxs-lookup"><span data-stu-id="cef2f-118">When cascading behaviors happen</span></span>

<span data-ttu-id="cef2f-119">カスケード削除は、依存または子エンティティを現在のプリンシパルまたは親と関連付けることができなくなった場合に必要になります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="cef2f-120">これは、プリンシパルまたは親が削除されたことが原因で発生することがあります。または、プリンシパルまたは親はまだ存在しているものの、依存元または子が関連付けられた状態でなくなった場合に、発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="cef2f-121">プリンシパルまたは親の削除</span><span class="sxs-lookup"><span data-stu-id="cef2f-121">Deleting a principal/parent</span></span>

<span data-ttu-id="cef2f-122">このような単純なモデルを考えてみます。つまり、`Blog` がリレーションシップのプリンシパルまたは親であり、`Post` が依存元または子であるとします。</span><span class="sxs-lookup"><span data-stu-id="cef2f-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="cef2f-123">`Post.BlogId` は外部キー プロパティで、その値は、ブログが属する投稿の `Post.Id` 主キーと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="cef2f-124">規則により、`Post.BlogId` 外部キー プロパティは null 非許容であるため、このリレーションシップは必須として構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="cef2f-125">必須のリレーションシップは、既定でカスケード削除を使用するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="cef2f-126">リレーションシップのモデリングの詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="cef2f-127">ブログを削除すると、すべての投稿がカスケード削除されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="cef2f-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="cef2f-129">例として SQL Server を使用すると、SaveChanges によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="cef2f-130">リレーションシップの切断</span><span class="sxs-lookup"><span data-stu-id="cef2f-130">Severing a relationship</span></span>

<span data-ttu-id="cef2f-131">ブログを削除するのではなく、各投稿とブログとの間のリレーションシップを切断することができます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="cef2f-132">これを行うには、各投稿の参照ナビゲーション `Post.Blog` を null 値に設定します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="cef2f-133">また、`Blog.Posts` コレクション ナビゲーションから各投稿を削除することによってリレーションシップを切断することもできます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="cef2f-134">どちらの場合も、結果は同じです。ブログは削除されませんが、どのブログとも関連付けられなくなった投稿は削除されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="cef2f-135">プリンシパルまたは依存元と関連付けられなくなったエンティティを削除することは、"孤立の削除" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="cef2f-136">カスケード削除と孤立の削除は密接に関連しています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="cef2f-137">どちらの場合も、必須のプリンシパルまたは親とのリレーションシップが切断されると、依存または子エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="cef2f-138">カスケード削除の場合、この切断は、プリンシパルまたは親自体が削除されたことが原因で発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="cef2f-139">孤立の場合、プリンシパルまたは親エンティティは依然として存在しますが、依存または子エンティティとの関連付けはなくなっています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="cef2f-140">カスケード動作が発生する場所</span><span class="sxs-lookup"><span data-stu-id="cef2f-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="cef2f-141">カスケード動作は以下に対して適用できます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="cef2f-142">現在の <xref:Microsoft.EntityFrameworkCore.DbContext> によって追跡されるエンティティ</span><span class="sxs-lookup"><span data-stu-id="cef2f-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="cef2f-143">コンテキストに読み込まれていないデータベース内のエンティティ</span><span class="sxs-lookup"><span data-stu-id="cef2f-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="cef2f-144">追跡対象エンティティのカスケード削除</span><span class="sxs-lookup"><span data-stu-id="cef2f-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="cef2f-145">EF Core を使用すると、構成済みのカスケード動作は常に追跡対象エンティティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="cef2f-146">これが意味することは、上記の例に示すように、アプリケーションで関連するすべての依存または子エンティティを DbContext に読み込むと、データベースの構成方法に関係なく、カスケード動作が正しく適用されるということです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="cef2f-147">追跡対象エンティティに対してカスケード動作が発生する厳密なタイミングは、<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> と <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cef2f-148">詳細については、「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="cef2f-149">データベースでのカスケード削除</span><span class="sxs-lookup"><span data-stu-id="cef2f-149">Cascade delete in the database</span></span>

<span data-ttu-id="cef2f-150">多くのデータベース システムにも、データベース内でエンティティが削除されたときにトリガーされるカスケード動作があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="cef2f-151">EF Core の場合、このような動作は、<xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> または EF Core 移行を使用したデータベース作成時に、EF Core モデルのカスケード削除動作に基づいて構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="cef2f-152">たとえば、上記のモデルを使用し、SQL Server を使用する場合、次のテーブルが投稿用に作成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="cef2f-153">ブログと投稿との間のリレーションシップを定義する外部キー制約が `ON DELETE CASCADE` を使用して構成されていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="cef2f-154">データベースがこのように構成されていることがわかっている場合は、"_最初に投稿を読み込むことなく_" ブログを削除できます。すると、そのブログに関連するすべての投稿がデータベースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="cef2f-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="cef2f-156">`Include` がないため投稿が読み込まれていないことに注目してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="cef2f-157">この場合、SaveChanges によってブログだけが削除されます。それが追跡されている唯一のエンティティであるためです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="cef2f-158">これにより、データベース内の外部キー制約がカスケード削除用に構成されていない場合は例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="cef2f-159">ただし、この場合は、データベース作成時に `ON DELETE CASCADE` が構成されているため、投稿が削除されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="cef2f-160">通常、データベースには孤立を自動的に削除する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="cef2f-161">これは、EF Core では外部キーに加えてナビゲーションを使用してリレーションシップを表すのに対し、データベースは外部キーのみを使用し、ナビゲーションがないためです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="cef2f-162">これは、通常、両方の側を DbContext に読み込まないとリレーションシップを切断できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="cef2f-163">現在、EF Core のインメモリ データベースについては、データベース内でのカスケード削除はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="cef2f-164">エンティティを論理的に削除する場合は、データベースでのカスケード削除を構成しないでください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="cef2f-165">エンティティが論理的に削除されるのではなく、誤って実際に削除されるおそれがあります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="cef2f-166">データベースのカスケードの制限事項</span><span class="sxs-lookup"><span data-stu-id="cef2f-166">Database cascade limitations</span></span>

<span data-ttu-id="cef2f-167">一部のデータベース (特に SQL Server) には、循環を形成するカスケード動作に関する制限があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="cef2f-168">たとえば、次のモデルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="cef2f-169">このモデルには 3 つのリレーションシップがあり、すべて必須であるため、規則に従ってカスケード削除を行うように構成されています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="cef2f-170">ブログを削除すると、関連するすべての投稿がカスケード削除されます</span><span class="sxs-lookup"><span data-stu-id="cef2f-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="cef2f-171">投稿の作成者を削除すると、作成された投稿がカスケード削除されます</span><span class="sxs-lookup"><span data-stu-id="cef2f-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="cef2f-172">ブログの所有者を削除すると、ブログがカスケード削除されます</span><span class="sxs-lookup"><span data-stu-id="cef2f-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="cef2f-173">これはすべて (ブログ管理ポリシーとしては多少厳密でも) 妥当ですが、これらのカスケードが構成された SQL Server データベースを作成しようとすると、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="cef2f-174">Microsoft.Data.SqlClient.SqlException (0x80131904): テーブル 'Posts' に FOREIGN KEY 制約 'FK_Posts_Person_AuthorId' を設定すると、パスの循環や、複数のカスケード パスが生じる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="cef2f-175">ON DELETE NO ACTION、ON UPDATE NO ACTION、を指定するか、他の FOREIGN KEY 制約を変更してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="cef2f-176">この状況に対処するには、2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="cef2f-177">削除をカスケードしないように 1 つまたは複数のリレーションシップを変更します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="cef2f-178">これらのカスケード削除の 1 つ以上を使用せずにデータベースを構成したうえで、EF Core でカスケード動作を実行できるようにするために、確実にすべての依存エンティティが読み込まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="cef2f-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="cef2f-179">この例で最初の方法を使用すると、Null 許容の外部キー プロパティを指定することで、ブログと所有者のリレーションシップを省略可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="cef2f-180">省略可能なリレーションシップを使用すると、所有者のないブログを存在させることができます。つまり、カスケード削除は既定では構成されなくなります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="cef2f-181">これは、カスケード アクションの循環がなくなったことを意味し、SQL Server でエラーを発生させずにデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="cef2f-182">代わりに 2 番目の方法を採用すると、ブログと所有者とのリレーションシップを必須でカスケード削除の対象として構成したままで、この構成をデータベースではなく追跡対象エンティティのみに適用するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="cef2f-183">では、人物とその人が所有するブログの両方を読み込んでから、その人物を削除すると、どうなるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="cef2f-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="cef2f-184">EF Core の場合、ブログも削除されるように所有者の削除がカスケードされます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="cef2f-185">ただし、所有者が削除されたときにブログが読み込まれていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="cef2f-186">次に、データベース内の外部キー制約違反が原因で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="cef2f-187">Microsoft.Data.SqlClient.SqlException: DELETE ステップは REFERENCE 制約 "FK_Blogs_People_OwnerId" と競合しています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="cef2f-188">競合が発生したのは、データベース "Scratch"、テーブル "dbo.Blogs"、列 'OwnerId' です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="cef2f-189">ステートメントは終了されました。</span><span class="sxs-lookup"><span data-stu-id="cef2f-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="cef2f-190">null 値のカスケード</span><span class="sxs-lookup"><span data-stu-id="cef2f-190">Cascading nulls</span></span>

<span data-ttu-id="cef2f-191">省略可能なリレーションシップには、Null 許容データベース列にマップされた Null 許容の外部キー プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="cef2f-192">これは、現在のプリンシパルまたは親が削除されたか、依存元または子から切断された場合に、外部キーの値を null 値に設定できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="cef2f-193">「[カスケード動作が発生するタイミング](#when-cascading-behaviors-happen)」の例をもう一度見てみましょう。ただし今度は、Null 許容の `Post.BlogId` 外部キー プロパティによって表される省略可能なリレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="cef2f-194">各投稿について、この外部キー プロパティは、関連するブログが削除されると null 値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="cef2f-195">たとえば、次のコードは以前と同じものです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="cef2f-196">SaveChanges が呼び出されると、結果として次のデータベース更新が行われます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="cef2f-197">同様に、上記の例のいずれかを使用してリレーションシップが切断された場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="cef2f-198">または:</span><span class="sxs-lookup"><span data-stu-id="cef2f-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="cef2f-199">次に、SaveChanges が呼び出されると、投稿は null 外部キー値で更新されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="cef2f-200">外部キーとナビゲーションが、値が変更されたときに EF Core でどのように管理されるかについて詳しくは、「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="cef2f-201">このようなリレーションシップの修正は、2008 年の最初のバージョンから Entity Framework の既定の動作でした。</span><span class="sxs-lookup"><span data-stu-id="cef2f-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="cef2f-202">EF Core より前は、それには名前がなく、変更することはできませんでした。</span><span class="sxs-lookup"><span data-stu-id="cef2f-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="cef2f-203">現在は、次のセクションで説明するように `ClientSetNull` として知られています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="cef2f-204">また、省略可能なリレーションシップのプリンシパルまたは親が削除されたときに、これと同様に null 値をカスケードするようにデータベースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="cef2f-205">ただしこれは、データベースでカスケード削除を使用することに比べると、あまり一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="cef2f-206">データベースで削除のカスケードと null 値のカスケードを同時に使用すると、ほとんどの場合、SQL Server 使用時にリレーションシップの循環が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="cef2f-207">null 値のカスケードを構成する方法の詳細については、次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="cef2f-208">カスケード動作の構成</span><span class="sxs-lookup"><span data-stu-id="cef2f-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="cef2f-209">ここに進む前に、上記のセクションを必ずお読みください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="cef2f-210">ここまでの資料を理解してからでないと、構成オプションがわかりにくくなる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="cef2f-211">カスケード動作は、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> の <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> メソッドを使用して、リレーションシップごとに構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="cef2f-212">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="cef2f-213">エンティティ型の間にリレーションシップを構成する方法の詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="cef2f-214">`OnDelete` には、明らかに紛らわしい <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> 列挙型から値が受け渡されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="cef2f-215">この列挙型により、追跡対象のエンティティに対する EF Core の動作と、EF を使用してスキーマを作成するときのデータベースでのカスケード削除の構成の両方を定義します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="cef2f-216">データベース スキーマへの影響</span><span class="sxs-lookup"><span data-stu-id="cef2f-216">Impact on database schema</span></span>

<span data-ttu-id="cef2f-217">次の表は、EF Core 移行または <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> によって作成された外部キー制約での各 `OnDelete` 値の結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="cef2f-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="cef2f-218">DeleteBehavior</span></span>        | <span data-ttu-id="cef2f-219">データベース スキーマへの影響</span><span class="sxs-lookup"><span data-stu-id="cef2f-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="cef2f-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-220">Cascade</span></span>               | <span data-ttu-id="cef2f-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="cef2f-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="cef2f-222">制限</span><span class="sxs-lookup"><span data-stu-id="cef2f-222">Restrict</span></span>              | <span data-ttu-id="cef2f-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="cef2f-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="cef2f-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-224">NoAction</span></span>              | <database default>
| <span data-ttu-id="cef2f-225">SetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-225">SetNull</span></span>               | <span data-ttu-id="cef2f-226">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="cef2f-226">ON DELETE SET NULL</span></span>
| <span data-ttu-id="cef2f-227">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-227">ClientSetNull</span></span>         | <span data-ttu-id="cef2f-228">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="cef2f-228">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="cef2f-229">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-229">ClientCascade</span></span>         | <span data-ttu-id="cef2f-230">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="cef2f-230">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="cef2f-231">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-231">ClientNoAction</span></span>        | <database default>

> [!NOTE]
> <span data-ttu-id="cef2f-232">この表は混乱しやすいため、今後のリリースで再検討される予定です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-232">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="cef2f-233">[GitHub のイシュー #21252](https://github.com/dotnet/efcore/issues/21252) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-233">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="cef2f-234">リレーショナル データベースでの `ON DELETE NO ACTION` と `ON DELETE RESTRICT` の動作は、通常は同一であるか非常に似ています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-234">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="cef2f-235">`NO ACTION` が示唆することとは異なり、これらの両方のオプションによって参照に関する制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-235">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="cef2f-236">相違点は、データベースで "_いつ_" 制約がチェックされるかです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-236">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="cef2f-237">使用しているデータベース システムでの `ON DELETE NO ACTION` と `ON DELETE RESTRICT` の具体的な違いについては、そのデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef2f-237">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="cef2f-238">データベースでカスケード動作を発生させる値は、`Cascade` と `SetNull` だけです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-238">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="cef2f-239">その他はすべて、どの変更もカスケードさせないようにデータベースを構成する値です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-239">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="cef2f-240">SaveChanges 動作への影響</span><span class="sxs-lookup"><span data-stu-id="cef2f-240">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="cef2f-241">以下のセクションの表では、プリンシパルまたは親が削除されたか、依存または子エンティティとのリレーションシップが切断された場合に、依存または子エンティティに何が起きるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-241">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="cef2f-242">各表は次のいずれかに対応しています。</span><span class="sxs-lookup"><span data-stu-id="cef2f-242">Each table covers one of:</span></span>

- <span data-ttu-id="cef2f-243">省略可能 (Null 許容の FK) と必須 (null 非許容の FK) のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cef2f-243">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="cef2f-244">依存元または子が、DbContext によって読み込まれて追跡される場合と、データベース内にのみ存在する場合</span><span class="sxs-lookup"><span data-stu-id="cef2f-244">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="cef2f-245">依存元または子が読み込まれる必須のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cef2f-245">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="cef2f-246">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="cef2f-246">DeleteBehavior</span></span>    | <span data-ttu-id="cef2f-247">プリンシパルまたは親が削除されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-247">On deleting principal/parent</span></span>             | <span data-ttu-id="cef2f-248">プリンシパルまたは親から切断されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-248">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="cef2f-249">Cascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-249">Cascade</span></span>           | <span data-ttu-id="cef2f-250">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-250">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="cef2f-251">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-251">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="cef2f-252">制限</span><span class="sxs-lookup"><span data-stu-id="cef2f-252">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="cef2f-253">NoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-253">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="cef2f-254">SetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-254">SetNull</span></span>           | <span data-ttu-id="cef2f-255">データベース作成時の `SqlException`</span><span class="sxs-lookup"><span data-stu-id="cef2f-255">`SqlException` on creating database</span></span>      | <span data-ttu-id="cef2f-256">データベース作成時の `SqlException`</span><span class="sxs-lookup"><span data-stu-id="cef2f-256">`SqlException` on creating database</span></span>
| <span data-ttu-id="cef2f-257">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-257">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="cef2f-258">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-258">ClientCascade</span></span>     | <span data-ttu-id="cef2f-259">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-259">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="cef2f-260">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-260">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="cef2f-261">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-261">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="cef2f-262">メモ:</span><span class="sxs-lookup"><span data-stu-id="cef2f-262">Notes:</span></span>

- <span data-ttu-id="cef2f-263">このような必須リレーションシップの既定値は `Cascade` です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-263">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="cef2f-264">必須リレーションシップに対してカスケード削除以外を使用すると、SaveChanges が呼び出されたときに例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-264">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="cef2f-265">通常、これは EF Core からの `InvalidOperationException` です。原因は、読み込まれた子または依存元に無効な状態が検出されたことです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-265">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="cef2f-266">`ClientNoAction` を使用すると、EF Core に、修正の依存元をデータベースに送信される前にチェックしないように強制できます。この場合、例外がデータベースからスローされ、SaveChanges によって `DbUpdateException` にラップされます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-266">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="cef2f-267">外部キー列は Null 許容ではないため、`SetNull` はデータベース作成時に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-267">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="cef2f-268">依存元または子は読み込まれるため、常に EF Core によって削除され、データベースによる削除の対象として残ることはありません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-268">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="cef2f-269">依存元または子が読み込まれていない必須リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cef2f-269">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="cef2f-270">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="cef2f-270">DeleteBehavior</span></span>    | <span data-ttu-id="cef2f-271">プリンシパルまたは親が削除されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-271">On deleting principal/parent</span></span>             | <span data-ttu-id="cef2f-272">プリンシパルまたは親から切断されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-272">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="cef2f-273">Cascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-273">Cascade</span></span>           | <span data-ttu-id="cef2f-274">データベースによって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-274">Dependents deleted by database</span></span>           | <span data-ttu-id="cef2f-275">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-275">N/A</span></span>
| <span data-ttu-id="cef2f-276">制限</span><span class="sxs-lookup"><span data-stu-id="cef2f-276">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="cef2f-277">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-277">N/A</span></span>
| <span data-ttu-id="cef2f-278">NoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-278">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="cef2f-279">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-279">N/A</span></span>
| <span data-ttu-id="cef2f-280">SetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-280">SetNull</span></span>           | <span data-ttu-id="cef2f-281">データベース作成時の `SqlException`</span><span class="sxs-lookup"><span data-stu-id="cef2f-281">`SqlException` on creating database</span></span>      | <span data-ttu-id="cef2f-282">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-282">N/A</span></span>
| <span data-ttu-id="cef2f-283">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-283">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="cef2f-284">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-284">N/A</span></span>
| <span data-ttu-id="cef2f-285">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-285">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="cef2f-286">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-286">N/A</span></span>
| <span data-ttu-id="cef2f-287">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-287">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="cef2f-288">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-288">N/A</span></span>

<span data-ttu-id="cef2f-289">メモ:</span><span class="sxs-lookup"><span data-stu-id="cef2f-289">Notes:</span></span>

- <span data-ttu-id="cef2f-290">リレーションシップの切断は、依存元または子が読み込まれていないため、ここでは無効です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-290">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="cef2f-291">このような必須リレーションシップの既定値は `Cascade` です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-291">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="cef2f-292">必須リレーションシップに対してカスケード削除以外を使用すると、SaveChanges が呼び出されたときに例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="cef2f-292">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="cef2f-293">これは通常、`DbUpdateException` です。原因は、依存元または子が読み込まれていないために、無効な状態をデータベースでしか検出できないことです。</span><span class="sxs-lookup"><span data-stu-id="cef2f-293">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="cef2f-294">これにより、SaveChanges によって `DbUpdateException` にデータベース例外がラップされます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-294">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="cef2f-295">外部キー列は Null 許容ではないため、`SetNull` はデータベース作成時に拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-295">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="cef2f-296">依存元または子が読み込まれた省略可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cef2f-296">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="cef2f-297">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="cef2f-297">DeleteBehavior</span></span>    | <span data-ttu-id="cef2f-298">プリンシパルまたは親が削除されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-298">On deleting principal/parent</span></span>             | <span data-ttu-id="cef2f-299">プリンシパルまたは親から切断されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-299">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="cef2f-300">Cascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-300">Cascade</span></span>           | <span data-ttu-id="cef2f-301">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-301">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="cef2f-302">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-302">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="cef2f-303">制限</span><span class="sxs-lookup"><span data-stu-id="cef2f-303">Restrict</span></span>          | <span data-ttu-id="cef2f-304">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-304">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="cef2f-305">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-305">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="cef2f-306">NoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-306">NoAction</span></span>          | <span data-ttu-id="cef2f-307">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-307">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="cef2f-308">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-308">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="cef2f-309">SetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-309">SetNull</span></span>           | <span data-ttu-id="cef2f-310">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-310">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="cef2f-311">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-311">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="cef2f-312">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-312">ClientSetNull</span></span>     | <span data-ttu-id="cef2f-313">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-313">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="cef2f-314">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-314">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="cef2f-315">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-315">ClientCascade</span></span>     | <span data-ttu-id="cef2f-316">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-316">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="cef2f-317">EF Core によって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-317">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="cef2f-318">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-318">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="cef2f-319">依存元の FK が EF Core によって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-319">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="cef2f-320">メモ:</span><span class="sxs-lookup"><span data-stu-id="cef2f-320">Notes:</span></span>

- <span data-ttu-id="cef2f-321">このような省略可能なリレーションシップの既定値は `ClientSetNull` です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-321">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="cef2f-322">`Cascade` または `ClientCascade` が構成されていない限り、依存元または子は削除されません。</span><span class="sxs-lookup"><span data-stu-id="cef2f-322">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="cef2f-323">その他すべての値の場合は、依存元の FK が EF Core によって null 値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-323">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="cef2f-324">例外は `ClientNoAction` です。これを使用すると、プリンシパルまたは親が削除されたときに依存元または子の外部キーをそのままにするように EF Core に指示できます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-324">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="cef2f-325">このため、例外がデータベースからスローされ、SaveChanges によって `DbUpdateException` としてラップされます。</span><span class="sxs-lookup"><span data-stu-id="cef2f-325">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="cef2f-326">依存元または子が読み込まれていない省力可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cef2f-326">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="cef2f-327">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="cef2f-327">DeleteBehavior</span></span>    | <span data-ttu-id="cef2f-328">プリンシパルまたは親が削除されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-328">On deleting principal/parent</span></span>             | <span data-ttu-id="cef2f-329">プリンシパルまたは親から切断されたとき</span><span class="sxs-lookup"><span data-stu-id="cef2f-329">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="cef2f-330">Cascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-330">Cascade</span></span>           | <span data-ttu-id="cef2f-331">データベースによって依存元が削除される</span><span class="sxs-lookup"><span data-stu-id="cef2f-331">Dependents deleted by database</span></span>           | <span data-ttu-id="cef2f-332">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-332">N/A</span></span>
| <span data-ttu-id="cef2f-333">制限</span><span class="sxs-lookup"><span data-stu-id="cef2f-333">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="cef2f-334">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-334">N/A</span></span>
| <span data-ttu-id="cef2f-335">NoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-335">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="cef2f-336">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-336">N/A</span></span>
| <span data-ttu-id="cef2f-337">SetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-337">SetNull</span></span>           | <span data-ttu-id="cef2f-338">依存元の FK がデータベースによって null 値に設定される</span><span class="sxs-lookup"><span data-stu-id="cef2f-338">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="cef2f-339">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-339">N/A</span></span>
| <span data-ttu-id="cef2f-340">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="cef2f-340">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="cef2f-341">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-341">N/A</span></span>
| <span data-ttu-id="cef2f-342">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="cef2f-342">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="cef2f-343">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-343">N/A</span></span>
| <span data-ttu-id="cef2f-344">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="cef2f-344">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="cef2f-345">該当なし</span><span class="sxs-lookup"><span data-stu-id="cef2f-345">N/A</span></span>

<span data-ttu-id="cef2f-346">メモ:</span><span class="sxs-lookup"><span data-stu-id="cef2f-346">Notes:</span></span>

- <span data-ttu-id="cef2f-347">リレーションシップの切断は、依存元または子が読み込まれていないため、ここでは無効です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-347">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="cef2f-348">このような省略可能なリレーションシップの既定値は `ClientSetNull` です。</span><span class="sxs-lookup"><span data-stu-id="cef2f-348">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="cef2f-349">データベースが削除または null 値をカスケードするように構成されている場合を除き、データベースの例外を回避するには、依存元または子を読み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef2f-349">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>

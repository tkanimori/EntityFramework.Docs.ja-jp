---
title: 連鎖削除 - EF Core
description: エンティティがプリンシパルまたは親から切断されたときにトリガーされるカスケード動作の構成
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 27ba84fa5d7e0d72e66ccbd96df9b6a5008791fb
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983340"
---
# <a name="cascade-delete"></a>連鎖削除

Entity Framework Core (EF Core) において、リレーションシップは外部キーを使用して表されます。 外部キーを含むエンティティは、リレーションシップの子または依存エンティティです。 このエンティティの外部キー値は、関連するプリンシパルまたは親エンティティの主キー値 (または代替キー値) と一致する必要があります。

プリンシパルまたは親エンティティが削除された場合、依存元または子の外部キー値は、"_どの_" プリンシパルまたは親の主あるいは代替キーとも一致しなくなります。 これは無効な状態であり、ほとんどのデータベースで参照制約違反が発生します。

この参照に関する制約違反を回避するには、次の 2 つのオプションがあります。

1. FK 値を null 値に設定する
2. 依存または子エンティティも削除する

最初のオプションは、省略可能なリレーションシップに対してのみ有効であり、かつ外部キー プロパティ (およびマップ先のデータベース列) が Null 許容でなければなりません。

2 番目のオプションは、任意の種類のリレーションシップに対して有効であり、"カスケード削除" と呼ばれます。

> [!TIP]
> このドキュメントでは、データベースの更新の観点から、カスケード削除 (および孤立の削除) について説明します。 それと密接に関連する概念が「[EF Core での変更の追跡](xref:core/change-tracking/index)」および「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」で説明されています。 これらの概念を十分に理解してから、ここに示す内容を読み進めてください。

> [!TIP]  
> このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes)することで実行およびデバッグできます。

## <a name="when-cascading-behaviors-happen"></a>カスケード動作が発生するタイミング

カスケード削除は、依存または子エンティティを現在のプリンシパルまたは親と関連付けることができなくなった場合に必要になります。 これは、プリンシパルまたは親が削除されたことが原因で発生することがあります。または、プリンシパルまたは親はまだ存在しているものの、依存元または子が関連付けられた状態でなくなった場合に、発生することがあります。

### <a name="deleting-a-principalparent"></a>プリンシパルまたは親の削除

このような単純なモデルを考えてみます。つまり、`Blog` がリレーションシップのプリンシパルまたは親であり、`Post` が依存元または子であるとします。 `Post.BlogId` は外部キー プロパティで、その値は、ブログが属する投稿の `Post.Id` 主キーと一致する必要があります。

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

規則により、`Post.BlogId` 外部キー プロパティは null 非許容であるため、このリレーションシップは必須として構成されます。 必須のリレーションシップは、既定でカスケード削除を使用するように構成されます。 リレーションシップのモデリングの詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。

ブログを削除すると、すべての投稿がカスケード削除されます。 次に例を示します。

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

例として SQL Server を使用すると、SaveChanges によって次の SQL が生成されます。

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

### <a name="severing-a-relationship"></a>リレーションシップの切断

ブログを削除するのではなく、各投稿とブログとの間のリレーションシップを切断することができます。 これを行うには、各投稿の参照ナビゲーション `Post.Blog` を null 値に設定します。

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

また、`Blog.Posts` コレクション ナビゲーションから各投稿を削除することによってリレーションシップを切断することもできます。

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

どちらの場合も、結果は同じです。ブログは削除されませんが、どのブログとも関連付けられなくなった投稿は削除されます。

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

プリンシパルまたは依存元と関連付けられなくなったエンティティを削除することは、"孤立の削除" と呼ばれます。

> [!TIP]
> カスケード削除と孤立の削除は密接に関連しています。 どちらの場合も、必須のプリンシパルまたは親とのリレーションシップが切断されると、依存または子エンティティが削除されます。 カスケード削除の場合、この切断は、プリンシパルまたは親自体が削除されたことが原因で発生します。 孤立の場合、プリンシパルまたは親エンティティは依然として存在しますが、依存または子エンティティとの関連付けはなくなっています。  

## <a name="where-cascading-behaviors-happen"></a>カスケード動作が発生する場所

カスケード動作は以下に対して適用できます。

- 現在の <xref:Microsoft.EntityFrameworkCore.DbContext> によって追跡されるエンティティ
- コンテキストに読み込まれていないデータベース内のエンティティ

### <a name="cascade-delete-of-tracked-entities"></a>追跡対象エンティティのカスケード削除

EF Core を使用すると、構成済みのカスケード動作は常に追跡対象エンティティに適用されます。 これが意味することは、上記の例に示すように、アプリケーションで関連するすべての依存または子エンティティを DbContext に読み込むと、データベースの構成方法に関係なく、カスケード動作が正しく適用されるということです。

> [!TIP]
> 追跡対象エンティティに対してカスケード動作が発生する厳密なタイミングは、<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> と <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> を使用して制御できます。 詳細については、「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」を参照してください。

### <a name="cascade-delete-in-the-database"></a>データベースでのカスケード削除

多くのデータベース システムにも、データベース内でエンティティが削除されたときにトリガーされるカスケード動作があります。 EF Core の場合、このような動作は、<xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> または EF Core 移行を使用したデータベース作成時に、EF Core モデルのカスケード削除動作に基づいて構成されます。 たとえば、上記のモデルを使用し、SQL Server を使用する場合、次のテーブルが投稿用に作成されます。

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

ブログと投稿との間のリレーションシップを定義する外部キー制約が `ON DELETE CASCADE` を使用して構成されていることに注目してください。

データベースがこのように構成されていることがわかっている場合は、"_最初に投稿を読み込むことなく_" ブログを削除できます。すると、そのブログに関連するすべての投稿がデータベースで削除されます。 次に例を示します。

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

`Include` がないため投稿が読み込まれていないことに注目してください。 この場合、SaveChanges によってブログだけが削除されます。それが追跡されている唯一のエンティティであるためです。

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

これにより、データベース内の外部キー制約がカスケード削除用に構成されていない場合は例外が発生します。 ただし、この場合は、データベース作成時に `ON DELETE CASCADE` が構成されているため、投稿が削除されます。

> [!NOTE]
> 通常、データベースには孤立を自動的に削除する方法はありません。 これは、EF Core では外部キーに加えてナビゲーションを使用してリレーションシップを表すのに対し、データベースは外部キーのみを使用し、ナビゲーションがないためです。 これは、通常、両方の側を DbContext に読み込まないとリレーションシップを切断できないことを意味します。

> [!NOTE]
> 現在、EF Core のインメモリ データベースについては、データベース内でのカスケード削除はサポートされていません。

> [!WARNING]
> エンティティを論理的に削除する場合は、データベースでのカスケード削除を構成しないでください。 エンティティが論理的に削除されるのではなく、誤って実際に削除されるおそれがあります。

### <a name="database-cascade-limitations"></a>データベースのカスケードの制限事項

一部のデータベース (特に SQL Server) には、循環を形成するカスケード動作に関する制限があります。 たとえば、次のモデルについて考えます。

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

このモデルには 3 つのリレーションシップがあり、すべて必須であるため、規則に従ってカスケード削除を行うように構成されています。

- ブログを削除すると、関連するすべての投稿がカスケード削除されます
- 投稿の作成者を削除すると、作成された投稿がカスケード削除されます
- ブログの所有者を削除すると、ブログがカスケード削除されます

これはすべて (ブログ管理ポリシーとしては多少厳密でも) 妥当ですが、これらのカスケードが構成された SQL Server データベースを作成しようとすると、次の例外が発生します。

> Microsoft.Data.SqlClient.SqlException (0x80131904): テーブル 'Posts' に FOREIGN KEY 制約 'FK_Posts_Person_AuthorId' を設定すると、パスの循環や、複数のカスケード パスが生じる場合があります。 ON DELETE NO ACTION、ON UPDATE NO ACTION、を指定するか、他の FOREIGN KEY 制約を変更してください。

この状況に対処するには、2 つの方法があります。

1. 削除をカスケードしないように 1 つまたは複数のリレーションシップを変更します。
2. これらのカスケード削除の 1 つ以上を使用せずにデータベースを構成したうえで、EF Core でカスケード動作を実行できるようにするために、確実にすべての依存エンティティが読み込まれるようにします。

この例で最初の方法を使用すると、Null 許容の外部キー プロパティを指定することで、ブログと所有者のリレーションシップを省略可能にすることができます。

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

省略可能なリレーションシップを使用すると、所有者のないブログを存在させることができます。つまり、カスケード削除は既定では構成されなくなります。 これは、カスケード アクションの循環がなくなったことを意味し、SQL Server でエラーを発生させずにデータベースを作成できます。

代わりに 2 番目の方法を採用すると、ブログと所有者とのリレーションシップを必須でカスケード削除の対象として構成したままで、この構成をデータベースではなく追跡対象エンティティのみに適用するように構成できます。

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

では、人物とその人が所有するブログの両方を読み込んでから、その人物を削除すると、どうなるでしょうか。

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core の場合、ブログも削除されるように所有者の削除がカスケードされます。

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

ただし、所有者が削除されたときにブログが読み込まれていない場合は、次のようになります。

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

次に、データベース内の外部キー制約違反が原因で例外がスローされます。

> Microsoft.Data.SqlClient.SqlException: DELETE ステップは REFERENCE 制約 "FK_Blogs_People_OwnerId" と競合しています。 競合が発生したのは、データベース "Scratch"、テーブル "dbo.Blogs"、列 'OwnerId' です。
ステートメントは終了されました。

## <a name="cascading-nulls"></a>null 値のカスケード

省略可能なリレーションシップには、Null 許容データベース列にマップされた Null 許容の外部キー プロパティが含まれています。 これは、現在のプリンシパルまたは親が削除されたか、依存元または子から切断された場合に、外部キーの値を null 値に設定できることを意味します。

「[カスケード動作が発生するタイミング](#when-cascading-behaviors-happen)」の例をもう一度見てみましょう。ただし今度は、Null 許容の `Post.BlogId` 外部キー プロパティによって表される省略可能なリレーションシップを使用します。

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

各投稿について、この外部キー プロパティは、関連するブログが削除されると null 値に設定されます。 たとえば、次のコードは以前と同じものです。

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

SaveChanges が呼び出されると、結果として次のデータベース更新が行われます。

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

同様に、上記の例のいずれかを使用してリレーションシップが切断された場合は、次のようになります。

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

または:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

次に、SaveChanges が呼び出されると、投稿は null 外部キー値で更新されます。

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

外部キーとナビゲーションが、値が変更されたときに EF Core でどのように管理されるかについて詳しくは、「[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)」を参照してください。

> [!NOTE]
> このようなリレーションシップの修正は、2008 年の最初のバージョンから Entity Framework の既定の動作でした。 EF Core より前は、それには名前がなく、変更することはできませんでした。 現在は、次のセクションで説明するように `ClientSetNull` として知られています。

また、省略可能なリレーションシップのプリンシパルまたは親が削除されたときに、これと同様に null 値をカスケードするようにデータベースを構成することもできます。 ただしこれは、データベースでカスケード削除を使用することに比べると、あまり一般的ではありません。 データベースで削除のカスケードと null 値のカスケードを同時に使用すると、ほとんどの場合、SQL Server 使用時にリレーションシップの循環が発生します。 null 値のカスケードを構成する方法の詳細については、次のセクションを参照してください。

## <a name="configuring-cascading-behaviors"></a>カスケード動作の構成

> [!TIP]
> ここに進む前に、上記のセクションを必ずお読みください。 ここまでの資料を理解してからでないと、構成オプションがわかりにくくなる場合があります。

カスケード動作は、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> の <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> メソッドを使用して、リレーションシップごとに構成されます。 次に例を示します。

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

エンティティ型の間にリレーションシップを構成する方法の詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。

`OnDelete` には、明らかに紛らわしい <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> 列挙型から値が受け渡されます。 この列挙型により、追跡対象のエンティティに対する EF Core の動作と、EF を使用してスキーマを作成するときのデータベースでのカスケード削除の構成の両方を定義します。

### <a name="impact-on-database-schema"></a>データベース スキーマへの影響

次の表は、EF Core 移行または <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> によって作成された外部キー制約での各 `OnDelete` 値の結果を示しています。

| DeleteBehavior        | データベース スキーマへの影響
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| 制限              | ON DELETE NO ACTION
| NoAction              | データベースの既定値
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | データベースの既定値

> [!NOTE]
> この表は混乱しやすいため、今後のリリースで再検討される予定です。 [GitHub のイシュー #21252](https://github.com/dotnet/efcore/issues/21252) を参照してください。

リレーショナル データベースでの `ON DELETE NO ACTION` と `ON DELETE RESTRICT` の動作は、通常は同一であるか非常に似ています。 `NO ACTION` が示唆することとは異なり、これらの両方のオプションによって参照に関する制約が適用されます。 相違点は、データベースで "_いつ_" 制約がチェックされるかです。  使用しているデータベース システムでの `ON DELETE NO ACTION` と `ON DELETE RESTRICT` の具体的な違いについては、そのデータベースのドキュメントを参照してください。

データベースでカスケード動作を発生させる値は、`Cascade` と `SetNull` だけです。 その他はすべて、どの変更もカスケードさせないようにデータベースを構成する値です。

### <a name="impact-on-savechanges-behavior"></a>SaveChanges 動作への影響

以下のセクションの表では、プリンシパルまたは親が削除されたか、依存または子エンティティとのリレーションシップが切断された場合に、依存または子エンティティに何が起きるかについて説明します。 各表は次のいずれかに対応しています。

- 省略可能 (Null 許容の FK) と必須 (null 非許容の FK) のリレーションシップ
- 依存元または子が、DbContext によって読み込まれて追跡される場合と、データベース内にのみ存在する場合

#### <a name="required-relationship-with-dependentschildren-loaded"></a>依存元または子が読み込まれる必須のリレーションシップ

| DeleteBehavior    | プリンシパルまたは親が削除されたとき             | プリンシパルまたは親から切断されたとき
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core によって依存元が削除される            | EF Core によって依存元が削除される
| 制限          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | データベース作成時の `SqlException`      | データベース作成時の `SqlException`
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | EF Core によって依存元が削除される            | EF Core によって依存元が削除される
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

メモ:

- このような必須リレーションシップの既定値は `Cascade` です。
- 必須リレーションシップに対してカスケード削除以外を使用すると、SaveChanges が呼び出されたときに例外が発生します。
  - 通常、これは EF Core からの `InvalidOperationException` です。原因は、読み込まれた子または依存元に無効な状態が検出されたことです。
  - `ClientNoAction` を使用すると、EF Core に、修正の依存元をデータベースに送信される前にチェックしないように強制できます。この場合、例外がデータベースからスローされ、SaveChanges によって `DbUpdateException` にラップされます。
  - 外部キー列は Null 許容ではないため、`SetNull` はデータベース作成時に拒否されます。
- 依存元または子は読み込まれるため、常に EF Core によって削除され、データベースによる削除の対象として残ることはありません。

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>依存元または子が読み込まれていない必須リレーションシップ

| DeleteBehavior    | プリンシパルまたは親が削除されたとき             | プリンシパルまたは親から切断されたとき
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | データベースによって依存元が削除される           | 該当なし
| 制限          | `DbUpdateException`                      | 該当なし
| NoAction          | `DbUpdateException`                      | 該当なし
| SetNull           | データベース作成時の `SqlException`      | 該当なし
| ClientSetNull     | `DbUpdateException`                      | 該当なし
| ClientCascade     | `DbUpdateException`                      | 該当なし
| ClientNoAction    | `DbUpdateException`                      | 該当なし

メモ:

- リレーションシップの切断は、依存元または子が読み込まれていないため、ここでは無効です。
- このような必須リレーションシップの既定値は `Cascade` です。
- 必須リレーションシップに対してカスケード削除以外を使用すると、SaveChanges が呼び出されたときに例外が発生します。
  - これは通常、`DbUpdateException` です。原因は、依存元または子が読み込まれていないために、無効な状態をデータベースでしか検出できないことです。 これにより、SaveChanges によって `DbUpdateException` にデータベース例外がラップされます。
  - 外部キー列は Null 許容ではないため、`SetNull` はデータベース作成時に拒否されます。

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>依存元または子が読み込まれた省略可能なリレーションシップ

| DeleteBehavior    | プリンシパルまたは親が削除されたとき             | プリンシパルまたは親から切断されたとき
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core によって依存元が削除される            | EF Core によって依存元が削除される
| 制限          | 依存元の FK が EF Core によって null 値に設定される     | 依存元の FK が EF Core によって null 値に設定される
| NoAction          | 依存元の FK が EF Core によって null 値に設定される     | 依存元の FK が EF Core によって null 値に設定される
| SetNull           | 依存元の FK が EF Core によって null 値に設定される     | 依存元の FK が EF Core によって null 値に設定される
| ClientSetNull     | 依存元の FK が EF Core によって null 値に設定される     | 依存元の FK が EF Core によって null 値に設定される
| ClientCascade     | EF Core によって依存元が削除される            | EF Core によって依存元が削除される
| ClientNoAction    | `DbUpdateException`                      | 依存元の FK が EF Core によって null 値に設定される

メモ:

- このような省略可能なリレーションシップの既定値は `ClientSetNull` です。
- `Cascade` または `ClientCascade` が構成されていない限り、依存元または子は削除されません。
- その他すべての値の場合は、依存元の FK が EF Core によって null 値に設定されます。
  - 例外は `ClientNoAction` です。これを使用すると、プリンシパルまたは親が削除されたときに依存元または子の外部キーをそのままにするように EF Core に指示できます。 このため、例外がデータベースからスローされ、SaveChanges によって `DbUpdateException` としてラップされます。

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>依存元または子が読み込まれていない省力可能なリレーションシップ

| DeleteBehavior    | プリンシパルまたは親が削除されたとき             | プリンシパルまたは親から切断されたとき
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | データベースによって依存元が削除される           | 該当なし
| 制限          | `DbUpdateException`                      | 該当なし
| NoAction          | `DbUpdateException`                      | 該当なし
| SetNull           | 依存元の FK がデータベースによって null 値に設定される    | 該当なし
| ClientSetNull     | `DbUpdateException`                      | 該当なし
| ClientCascade     | `DbUpdateException`                      | 該当なし
| ClientNoAction    | `DbUpdateException`                      | 該当なし

メモ:

- リレーションシップの切断は、依存元または子が読み込まれていないため、ここでは無効です。
- このような省略可能なリレーションシップの既定値は `ClientSetNull` です。
- データベースが削除または null 値をカスケードするように構成されている場合を除き、データベースの例外を回避するには、依存元または子を読み込む必要があります。

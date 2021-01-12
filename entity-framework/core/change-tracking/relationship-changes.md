---
title: 外部キーとナビゲーションの変更-EF Core
description: 外部キーとナビゲーションを操作してエンティティ間のリレーションシップを変更する方法
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: ac2110509b6748e85411dbb14989522465925ecf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129620"
---
# <a name="changing-foreign-keys-and-navigations"></a>外部キーとナビゲーションの変更

## <a name="overview-of-foreign-keys-and-navigations"></a>外部キーとナビゲーションの概要

Entity Framework Core (EF Core) モデルのリレーションシップは、外部キー (FKs) を使用して表されます。 FK は、リレーションシップの依存エンティティまたは子エンティティの1つ以上のプロパティで構成されます。 この依存/子エンティティは、特定のプリンシパル/親エンティティに関連付けられています。これは、依存/子の外部キープロパティの値が、プリンシパル/親の代替または主キー (PK) プロパティの値と一致する場合です。

外部キーは、データベース内のリレーションシップを格納および操作するのに適した方法ですが、アプリケーションコードで複数の関連エンティティを操作する場合にはあまりわかりません。 したがって、ほとんどの EF Core モデルでは、FK 表現に "ナビゲーション" がレイヤー化されます。 ナビゲーションフォーム C# では、外部キー値を主キー値または代替キー値に一致させることによって検出されたアソシエーションを反映するエンティティインスタンス間の参照を/.NET します。

ナビゲーションは、リレーションシップの両側でのみ使用できます。一方の側でのみ使用することも、FK プロパティだけを残すこともできます。 FK プロパティを [shadow プロパティ](xref:core/modeling/shadow-properties)に設定することにより、非表示にすることができます。 モデリング関係の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。

> [!TIP]
> このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。 これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

> [!TIP]
> [GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。

### <a name="example-model"></a>モデルの例

次のモデルには、リレーションシップを持つ4つのエンティティ型が含まれています。 コード内のコメントは、外部キー、主キー、およびナビゲーションであるプロパティを示します。

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

このモデルでは、次の3つのリレーションシップがあります。

- 各ブログには、多数の投稿を含めることができます (一対多)。
  - `Blog` プリンシパル/親を示します。
  - `Post` は、依存/子です。 これには、FK プロパティが含まれています。このプロパティ `Post.BlogId` の値は、関連するブログの PK 値と一致する必要があり `Blog.Id` ます。
  - `Post.Blog` は、関連するブログへの投稿からの参照ナビゲーションです。 `Post.Blog` は、の逆ナビゲーションです `Blog.Posts` 。
  - `Blog.Posts` は、ブログからすべての関連する投稿へのコレクションナビゲーションです。 `Blog.Posts` は、の逆ナビゲーションです `Post.Blog` 。
- 各ブログは、1つの資産 (1 対 1) を持つことができます。
  - `Blog` プリンシパル/親を示します。
  - `BlogAssets` は、依存/子です。 これには、FK プロパティが含まれています。このプロパティ `BlogAssets.BlogId` の値は、関連するブログの PK 値と一致する必要があり `Blog.Id` ます。
  - `BlogAssets.Blog` は、アセットから関連するブログへの参照ナビゲーションです。 `BlogAssets.Blog` は、の逆ナビゲーションです `Blog.Assets` 。
  - `Blog.Assets` は、ブログから関連付けられた資産への参照ナビゲーションです。 `Blog.Assets` は、の逆ナビゲーションです `BlogAssets.Blog` 。
- 各投稿は多くのタグを持つことができ、各タグには多数の投稿を含めることができます (多対多)。
  - 多対多リレーションシップは、2 1 対多の関係を超えるレイヤーです。 多対多リレーションシップについては、このドキュメントで後ほど説明します。
  - `Post.Tags` は、関連付けられているすべてのタグへの投稿からのコレクションナビゲーションです。 `Post.Tags` は、の逆ナビゲーションです `Tag.Posts` 。
  - `Tag.Posts` タグから、関連付けられているすべての投稿へのコレクションナビゲーションです。 `Tag.Posts` は、の逆ナビゲーションです `Post.Tags` 。

リレーションシップをモデル化して構成する方法の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。

## <a name="relationship-fixup"></a>リレーションシップの修正

EF Core は、外部キー値との連携を維持します。また、その逆も可能です。 つまり、外部キーの値が変更され、別のプリンシパル/親エンティティが参照されるようになった場合、この変更を反映するためにナビゲーションが更新されます。 同様に、ナビゲーションが変更された場合は、関連するエンティティの外部キー値が更新され、この変更が反映されます。 これは、"リレーションシップの修正" と呼ばれます。

### <a name="fixup-by-query"></a>クエリによる修正

Fixup は、エンティティがデータベースから照会されるときに最初に行われます。 データベースには外部キー値のみが含まれているので、EF Core がデータベースからエンティティインスタンスを作成する場合は、外部キー値を使用して参照ナビゲーションを設定し、必要に応じてエンティティをコレクションナビゲーションに追加します。 たとえば、ブログとそれに関連付けられている投稿と資産のクエリを考えてみましょう。

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

ブログごとに、最初にインスタンスを作成 EF Core `Blog` ます。 次に、各投稿がデータベースから読み込まれると、 `Post.Blog` 関連するブログをポイントするように参照ナビゲーションが設定されます。 同様に、投稿はコレクションのナビゲーションに追加され `Blog.Posts` ます。 同じことが発生し `BlogAssets` ます。ただし、この場合も、両方のナビゲーションが参照である点が異なります。 `Blog.Assets`ナビゲーションは assets インスタンスを指すように設定されており、 `BlogAsserts.Blog` ナビゲーションはブログインスタンスを指すように設定されています。

このクエリの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を見ると、2つのブログが表示されます。それぞれに1つの資産と2つの投稿が追跡されています。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

[デバッグ] ビューには、キー値とナビゲーションの両方が表示されます。 ナビゲーションは、関連エンティティの主キー値を使用して表示されます。 たとえば、 `Posts: [{Id: 1}, {Id: 2}]` 上記の出力では、 `Blog.Posts` コレクションナビゲーションに主キー1と2を持つ2つの関連する投稿が含まれていることを示しています。 同様に、最初のブログに関連付けられている投稿ごとに、この `Blog: {Id: 1}` 行はナビゲーションが主キー1のブログを参照していることを示して `Post.Blog` います。

### <a name="fixup-to-locally-tracked-entities"></a>ローカルで追跡されるエンティティへの修正

リレーションシップの修正は、追跡クエリから返されるエンティティと、DbContext によって既に追跡されているエンティティの間でも行われます。 たとえば、ブログ、投稿、および資産に対して3つの個別のクエリを実行することを検討してください。

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[! code-csharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ]もう一度デバッグビューを見て、最初のクエリが2つのブログだけを追跡した後に、次のように追跡します。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

`Blog.Assets`参照ナビゲーションは null で `Blog.Posts` あり、関連付けられているエンティティがコンテキストによって現在追跡されていないため、コレクションナビゲーションは空です。

2番目のクエリの後、 `Blogs.Assets` 参照ナビゲーションは、新しく追跡されたインスタンスを指すように修正されてい `BlogAsset` ます。 同様に、 `BlogAssets.Blog` 参照ナビゲーションは、既に追跡されている適切なインスタンスを指すように設定され `Blog` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

最後に、3番目のクエリの後に、 `Blog.Posts` コレクションのナビゲーションに関連するすべての投稿が含まれるようになり、 `Post.Blog` 参照が適切なインスタンスを指すようになりました `Blog` 。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

これは、元の単一のクエリで実現されたのと同じエンドステートです。これは、複数の異なるクエリを使用している場合でも、エンティティが追跡されていたため、EF Core は固定されているためです。

> [!NOTE]
> Fixup によって、データベースからより多くのデータが返されることはありません。 クエリによって既に返されているエンティティ、または既に DbContext によって追跡されているエンティティのみを接続します。 エンティティをシリアル化するときに重複を処理する方法については、「 [EF Core の Id 解決](xref:core/change-tracking/identity-resolution) 」を参照してください。

## <a name="changing-relationships-using-navigations"></a>ナビゲーションを使用したリレーションシップの変更

2つのエンティティ間のリレーションシップを変更する最も簡単な方法は、ナビゲーションを操作し、逆のナビゲーションと FK 値を適切に修正するために EF Core を残したままにすることです。 このためには、次のようにします。

- コレクションナビゲーションからのエンティティの追加または削除。
- 別のエンティティを指すように参照ナビゲーションを変更するか、null に設定します。

### <a name="adding-or-removing-from-collection-navigations"></a>コレクションナビゲーションの追加または削除

たとえば、Visual Studio ブログから .NET ブログに投稿の1つを移動してみましょう。 そのためには、最初にブログと投稿を読み込んでから、1つのブログのナビゲーションコレクションからもう一方のブログのナビゲーションコレクションに投稿を移動する必要があります。

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>ここでは、デバッグビューにアクセスしても、[変更の自動検出](xref:core/change-tracking/change-detection)が行われないため、への呼び出しが必要です。

上記のコードを実行した後に出力されるデバッグビューを次に示します。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

.NET ブログのナビゲーションには、 `Blog.Posts` 3 つの投稿 () が含まれるようになりました `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` 。 同様に、 `Blog.Posts` Visual Studio ブログのナビゲーションには、1つの投稿 () のみがあり `Posts: [{Id: 4}]` ます。 このコードでは、これらのコレクションが明示的に変更されているため、これは想定されています。

興味深いことに、コードが明示的にナビゲーションを変更しなかった場合でも、 `Post.Blog` Visual Studio ブログ () を指すように修正されてい `Blog: {Id: 1}` ます。 また、 `Post.BlogId` 外部キーの値は .net ブログの主キーの値と一致するように更新されています。 この変更は、SaveChanges が呼び出されたときに、の FK 値に変更され、データベースに保存されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>参照ナビゲーションの変更

前の例では、各ブログの投稿のコレクションナビゲーションを操作して、投稿があるブログから別のブログに移動されました。 代わりに、 `Post.Blog` 新しいブログを指すように参照ナビゲーションを変更することで、同じことを実現できます。 例:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

この変更の後のデバッグビューは、前の例と _まったく同じ_ です。 これは、EF Core によって参照ナビゲーションの変更が検出され、コレクションナビゲーションと FK 値が一致するように修正されたためです。

## <a name="changing-relationships-using-foreign-key-values"></a>外部キー値を使用したリレーションシップの変更

前のセクションでは、外部キーの値を自動的に更新するナビゲーションによってリレーションシップが操作されていました。 EF Core でリレーションシップを操作する場合は、この方法をお勧めします。 ただし、FK 値を直接操作することもできます。 たとえば、外部キーの値を変更することによって、投稿をあるブログから別のブログに移動でき `Post.BlogId` ます。

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

前の例で示したように、これが参照ナビゲーションの変更と非常によく似ていることに注意してください。

この変更後のデバッグビューは、前の2つの例の場合と _まったく同じ_ です。 これは、EF Core FK 値の変更が検出され、参照とコレクションの両方のナビゲーションが一致するように修正されたためです。

> [!TIP]
> リレーションシップが変更されるたびにすべてのナビゲーション値と FK 値を操作するコードを記述しないでください。 このようなコードはより複雑で、すべてのケースで外部キーとナビゲーションに対して一貫した変更を行う必要があります。 可能であれば、1つのナビゲーションを操作するか、両方の移動を操作するだけです。 必要に応じて、FK 値のみを操作します。 ナビゲーション値と FK 値の両方を操作しないようにします。

## <a name="fixup-for-added-or-deleted-entities"></a>追加または削除されたエンティティの修正

### <a name="adding-to-a-collection-navigation"></a>コレクションナビゲーションへの追加

EF Core は、新しい依存/子エンティティがコレクションのナビゲーションに追加されたことを [検出](xref:core/change-tracking/change-detection) すると、次のアクションを実行します。

- エンティティが追跡されていない場合は、追跡されます。 (エンティティは通常、状態になり `Added` ます。 ただし、生成されたキーを使用するようにエンティティ型が構成されていて、主キーの値が設定されている場合、エンティティは状態で追跡され `Unchanged` ます)。
- エンティティが別のプリンシパル/親に関連付けられている場合、そのリレーションシップは切断されます。
- エンティティは、コレクションナビゲーションを所有するプリンシパル/親に関連付けられます。
- ナビゲーションと外部キーの値は、関連するすべてのエンティティに対して固定されています。

これに基づいて、投稿をあるブログから別のブログに移動することができます。新しいに追加する前に、古いコレクションのナビゲーションから削除する必要はありません。 このため、上記の例のコードは次のように変更できます。

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

移動先:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core は、投稿が新しいブログに追加されていることを確認し、最初のブログのコレクションから自動的に削除します。

### <a name="removing-from-a-collection-navigation"></a>コレクションのナビゲーションからの削除

プリンシパル/親のコレクションナビゲーションから依存/子エンティティを削除すると、そのプリンシパル/親とのリレーションシップの切断が発生します。 次の処理は、リレーションシップが省略可能か必須かによって異なります。

#### <a name="optional-relationships"></a>省略可能なリレーションシップ

既定では、オプションのリレーションシップの場合、外部キーの値は null に設定されます。 これは、依存/子 _がプリンシパル/_ 親に関連付けられなくなったことを意味します。 たとえば、ブログや投稿を読み込んで、コレクションのナビゲーションから投稿の1つを削除してみましょう `Blog.Posts` 。

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

この変更後に、 [変更追跡デバッグビューを確認](xref:core/change-tracking/debug-views) すると、次のように表示されます。

- `Post.BlogId`FK が null () に設定されています `BlogId: <null> FK Modified Originally 1` 。
- `Post.Blog`参照のナビゲーションが null () に設定されています `Blog: <null>` 。
- 投稿は `Blog.Posts` コレクションナビゲーション () から削除されました `Posts: [{Id: 1}]`

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

投稿がとしてマークされて _いない_ ことに注意して `Deleted` ください。 `Modified`SaveChanges が呼び出されたときに、データベース内の FK 値が null に設定されるように、としてマークされています。

#### <a name="required-relationships"></a>必須リレーションシップ

FK 値を null に設定することはできません (通常は、必要なリレーションシップに対しては使用できません)。 したがって、必要なリレーションシップを切断するには、依存/子エンティティが新しいプリンシパル/親に再親されるか、または SaveChanges が呼び出されて参照制約違反が発生しないようにデータベースから削除される必要があります。 これは "孤立を削除しています" と呼ばれ、必要なリレーションシップの EF Core の既定の動作です。

たとえば、ブログと投稿の関係を必要に応じて変更し、前の例と同じコードを実行してみましょう。

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

この変更の後にデバッグビューを表示すると、次のようになります。

- この投稿は、 `Deleted` SaveChanges が呼び出されたときにデータベースから削除されるようにマークされています。
- `Post.Blog`参照ナビゲーションが null () に設定されてい `Blog: <null>` ます。
- 投稿は `Blog.Posts` コレクションナビゲーション () から削除されました `Posts: [{Id: 1}]` 。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

は、必要なリレーションシップがあるため、変更されていないことに注意し `Post.BlogId` てください。 null に設定することはできません。

SaveChanges を呼び出すと、孤立した投稿が削除されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>孤立したタイミングの削除と親の再親

既定では、 `Deleted` リレーションシップの変更が [検出](xref:core/change-tracking/change-detection)されるとすぐに、孤立をマークします。 ただし、このプロセスは、SaveChanges が実際に呼び出されるまで遅延させることができます。 これは、1つのプリンシパル/親から削除されたエンティティの孤立を回避するのに役立ちますが、SaveChanges が呼び出される前に、新しいプリンシパル/親で親が再設定されます。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> は、このタイミングを設定するために使用されます。 例:

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

最初のコレクションから投稿を削除した後、オブジェクトは前の例のようにマークされていません `Deleted` 。 この場合、 _必要なリレーションシップ_ があるにもかかわらず、リレーションシップが切断されていることを追跡しているのは EF Core です。 (FK 値は null と見なされますが、null になることはありませんが、型は null 値を許容しないため、null になることはありません EF Core。 これは "概念上の null" と呼ばれます)。

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

この時点で SaveChanges を呼び出すと、孤立した投稿が削除されます。 ただし、上記の例のように、SaveChanges が呼び出される前に、post が新しいブログに関連付けられている場合、その新しいブログに適切に修正され、孤立と見なされなくなります。

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

この時点で SaveChanges が呼び出されると、データベース内の投稿は削除されるのではなく更新されます。

孤立を自動的に削除しないようにすることもできます。 これにより、孤立したの追跡中に SaveChanges が呼び出された場合、例外が発生します。 たとえば、次のコードを使用します。

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

では、次の例外がスローされます。

> InvalidOperationException: エンティティ ' Blog ' と ' Post ' の間のキー値 ' {Blog Id: 1} ' の関連付けが切断されましたが、リレーションシップは必須としてマークされているか、外部キーが null 許容ではないため、暗黙的に必要です。 必要なリレーションシップが存在しないときに依存/子エンティティを削除する必要がある場合は、連鎖削除を使用するようにリレーションシップを構成します。

を呼び出すことにより、孤立した削除と連鎖削除をいつでも削除でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> ます。 これを、削除の孤立したタイミングをに設定して組み合わせると、 `Never` EF Core に明示的に指示されていない限り、孤立が削除されることはありません。

### <a name="changing-a-reference-navigation"></a>参照ナビゲーションの変更

一対多リレーションシップの参照ナビゲーションを変更すると、リレーションシップのもう一方の端のコレクションナビゲーションを変更するのと同じ効果があります。 依存/子の参照ナビゲーションを null に設定することは、プリンシパル/親のコレクションナビゲーションからエンティティを削除することと同じです。 すべての修正とデータベースの変更は、前のセクションで説明したように行われます。これには、リレーションシップが必要な場合にエンティティを孤立させることも含まれます。

#### <a name="optional-one-to-one-relationships"></a>省略可能な1対1のリレーションシップ

一対一のリレーションシップでは、参照ナビゲーションを変更すると、以前のリレーションシップが切断されます。 オプションのリレーションシップの場合、これは、前に関連付けられた依存/子の FK 値が null に設定されることを意味します。 例:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

SaveChanges を呼び出す前のデバッグビューでは、新しいアセットが既存のアセットを置き換えることを示しています。これは現在、null の FK 値でとマークされてい `Modified` `BlogAssets.BlogId` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

この結果、SaveChanges が呼び出されると、更新と挿入が行われます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a>必要な一対一のリレーションシップ

前の例と同じコードを実行していますが、今回は必要な1対1のリレーションシップを持つとして、以前に関連付けられていたが、新しいの場所では孤立している `BlogAssets` ため、としてマークされてい `Deleted` `BlogAssets` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

これにより、SaveChanges が呼び出されたときに、が削除され、が挿入されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

孤立を削除済みとしてマークするタイミングは、コレクションのナビゲーションと同じように変更でき、同じ効果があります。

### <a name="deleting-an-entity"></a>エンティティの削除

#### <a name="optional-relationships"></a>省略可能なリレーションシップ

エンティティがとしてマークされている場合 `Deleted` (たとえば、を呼び出すことによって)、削除された <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> エンティティへの参照は、他のエンティティのナビゲーションから削除されます。 オプションのリレーションシップでは、依存エンティティの FK 値は null に設定されます。

たとえば、Visual Studio ブログを次のようにマークし `Deleted` ます。

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

SaveChanges を呼び出す前に、 [変更トラッカーのデバッグビューを確認](xref:core/change-tracking/debug-views) すると、次のようになります。

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

次のことに注意してください。

- ブログはとしてマークされて `Deleted` います。
- 削除されたブログに関連する資産に、null の FK 値 ( `BlogId: <null> FK Modified Originally 2` ) と null 参照のナビゲーション () が含まれています。 `Blog: <null>`
- 削除されたブログに関連する各投稿には、null の FK 値 ( `BlogId: <null> FK Modified Originally 2` ) と null 参照のナビゲーション () があります。 `Blog: <null>`

#### <a name="required-relationships"></a>必須リレーションシップ

必要なリレーションシップの fixup 動作は、オプションのリレーションシップの場合と同じです。ただし、依存/子エンティティは、 `Deleted` プリンシパル/親がなければ存在できず、参照制約の例外を回避するために SaveChanges が呼び出されたときにデータベースから削除される必要があるためです。 これは "連鎖削除" と呼ばれ、必要なリレーションシップの EF Core の既定の動作です。 たとえば、前の例と同じコードを実行し、必要なリレーションシップを指定すると、SaveChanges が呼び出される前に、次のデバッグビューが表示されます。

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

予想どおり、依存/子はとしてマークされ `Deleted` ます。 ただし、削除されたエンティティのナビゲーションが変更されて _いない_ ことに注意してください。 これは奇妙に思えるかもしれませんが、すべてのナビゲーションをクリアすることで、削除されたエンティティのグラフを完全にシュレッダ処理することを回避できます つまり、ブログ、資産、および投稿は、削除後もエンティティのグラフを形成します。 これにより、グラフが細分化されている EF6 の場合よりも、エンティティのグラフの削除が大幅に簡単になります。

#### <a name="cascade-delete-timing-and-re-parenting"></a>連鎖削除のタイミングと再親

既定では、cascade delete は親/プリンシパルがとしてマークされるとすぐに発生し `Deleted` ます。 これは、前に説明したように、孤立を削除する場合と同じです。 孤立を削除するのと同様に、このプロセスは、SaveChanges が呼び出されるか、完全に無効にされるまで遅延することができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> ます。 これは、孤立を削除する場合と同じように役立ちます。たとえば、プリンシパル/親を削除した後で、子/依存関係を再親することができます。

連鎖削除、およびオーファンの削除は、を呼び出すことによっていつでも強制でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> ます。 連鎖削除のタイミングをに設定してこれを組み合わせると、 `Never` EF Core が明示的に指定されていない限り、連鎖削除は行われません。

> [!TIP]
> 連鎖削除および孤立削除は密接に関連しています。 どちらの場合も、必要なプリンシパル/親とのリレーションシップが切断されると、依存/子エンティティが削除されます。 Cascade delete の場合、この切断は、プリンシパル/親自体が削除されているために発生します。 孤立した場合、プリンシパル/親エンティティは依然として存在しますが、依存/子エンティティに関連付けられなくなります。

## <a name="many-to-many-relationships"></a>多対多のリレーションシップ

EF Core 内の多対多リレーションシップは、結合エンティティを使用して実装されます。 多対多リレーションシップは、一対多リレーションシップを持つこの結合エンティティに関連付けられています。 5.0 EF Core する前に、この結合エンティティは明示的に定義およびマップする必要がありました。 EF Core 5.0 以降では、暗黙的かつ非表示にすることができます。 ただし、どちらの場合も、基になる動作は同じです。 ここでは、この基になる動作を見て、多対多リレーションシップの追跡のしくみについて説明します。

### <a name="how-many-to-many-relationships-work"></a>多対多リレーションシップの動作

明示的に定義された結合エンティティ型を使用して、投稿とタグの間に多対多のリレーションシップを作成するこの EF Core モデルを考えてみましょう。

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

`PostTag`結合エンティティ型には、2つの外部キープロパティが含まれていることに注意してください。 このモデルでは、タグに関連する投稿の場合、 `PostTag.PostId` 外部キーの値が主キーの値と一致し、外部キーの値が主キーの値と一致するポストタグ結合エンティティが必要 `Post.Id` `PostTag.TagId` `Tag.Id` です。 例:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

このコードを実行した後に、 [変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views) を見ると、post とタグが新しい結合エンティティによって関連付けられていることがわかり `PostTag` ます。

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

とでのコレクションのナビゲーションが修正されていることに注意して `Post` `Tag` `PostTag` ください。 これらのリレーションシップは、前の例と同様に、FK 値ではなく、ナビゲーションによって操作できます。 たとえば、上記のコードを変更して、結合エンティティに対する参照ナビゲーションを設定することにより、リレーションシップを追加できます。

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

これにより、前の例のように、FKs とナビゲーションがまったく同じ変更になります。

### <a name="skip-navigations"></a>ナビゲーションのスキップ

> [!NOTE]
> スキップナビゲーションは EF Core 5.0 で導入されました。

結合テーブルの手動操作は煩雑になることがあります。 EF Core 5.0 以降、多対多リレーションシップは、結合エンティティを "スキップ" する特別なコレクションナビゲーションを使用して直接操作できます。 たとえば、上記のモデルには、2つのスキップナビゲーションを追加できます。1つは Post からタグに、もう1つはタグから投稿までです。

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

この多対多リレーションシップでは、スキップナビゲーションと通常のナビゲーションがすべて同じ多対多リレーションシップで使用されるようにするために、次の構成が必要です。

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

多対多リレーションシップのマッピングの詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。

ナビゲーションをスキップし、通常のコレクションのナビゲーションと同様に動作します。 ただし、外部キーの値を操作する方法は異なります。 投稿をタグに関連付けますが、今回はスキップナビゲーションを使用します。

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

このコードは join エンティティを使用しないことに注意してください。 代わりに、1対多のリレーションシップの場合と同じように、ナビゲーションコレクションにエンティティを追加するだけです。 結果のデバッグビューは、基本的には以前と同じです。

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

結合エンティティのインスタンスが `PostTag` 自動的に作成され、FK 値が、現在関連付けられているタグと post の PK 値に設定されていることに注意してください。 これらの FK 値に一致するように、通常の参照とコレクションのナビゲーションがすべて修正されました。 また、このモデルにはスキップナビゲーションが含まれているため、これらも修正されました。 具体的には、スキップナビゲーションにタグを追加した場合でも、 `Post.Tags` `Tag.Posts` このリレーションシップのもう一方の側の逆スキップナビゲーションも、関連付けられている投稿を含むように修正されています。

これは、スキップナビゲーションが上位に重なっている場合でも、基になる多対多リレーションシップを直接操作できることに注意してください。 たとえば、スキップナビゲーションを導入する前と同じようにタグと投稿を関連付けることができます。

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

または、FK 値を使用します。

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

この場合も、スキップナビゲーションが正しく修正されるため、前の例と同じデバッグビュー出力が生成されます。

### <a name="skip-navigations-only"></a>ナビゲーションのみスキップ

前のセクションでは、2つの基になる一対多リレーションシップ _を完全に定義するだけで_ なく、スキップナビゲーションを追加しました。 これは、FK 値に対して何が起こるかを示すのに便利ですが、多くの場合は不要です。 代わりに、 _スキップナビゲーションのみ_ を使用して多対多リレーションシップを定義できます。 このドキュメントの最上位にあるモデルでは、多対多リレーションシップが定義されています。 このモデルを使用すると、スキップナビゲーションに投稿を追加することによって、投稿とタグを再び関連付けることができ `Tag.Posts` ます (または、スキップナビゲーションにタグを追加することもでき `Post.Tags` ます)。

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

この変更を行った後にデバッグビューを確認すると、結合エンティティを表すのインスタンスが EF Core によって作成されていることがわかり `Dictionary<string, object>` ます。 この結合エンティティに `PostsId` は、関連付けられている `TagsId` post と TAG の PK 値に一致するように設定されているとの両方のキープロパティが含まれています。

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

暗黙の結合エンティティとエンティティ型の使用の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください `Dictionary<string, object>` 。

> [!IMPORTANT]
> 規則に従ってエンティティ型を結合するために使用される CLR 型は、パフォーマンスを向上させるために将来のリリースで変更される可能性があります。 `Dictionary<string, object>`このが明示的に構成されていない限り、結合の種類に依存することはできません。

### <a name="join-entities-with-payloads"></a>エンティティとペイロードの結合

ここまでの例では、多対多リレーションシップに必要な2つの外部キープロパティのみを含む結合エンティティ型 (明示的または暗黙的) が使用されていました。 これらの FK 値は、関連エンティティの主キープロパティから取得されるため、リレーションシップを操作するときに、アプリケーションによって明示的に設定する必要はありません。 これにより、データが欠落していなくても、結合エンティティのインスタンスを作成 EF Core ことができます。

#### <a name="payloads-with-generated-values"></a>生成された値を含むペイロード

EF Core では、結合エンティティ型へのプロパティの追加がサポートされています。 これは、結合エンティティに "ペイロード" を与えることと呼ばれます。 たとえば、 `TaggedOn` join エンティティにプロパティを追加してみましょう `PostTag` 。

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

このペイロードプロパティは、EF Core が結合エンティティインスタンスを作成するときには設定されません。 これに対処する最も一般的な方法は、ペイロードプロパティを自動的に生成された値と共に使用することです。 たとえば、 `TaggedOn` 新しいエンティティが挿入されるたびに、ストアによって生成されたタイムスタンプを使用するようにプロパティを構成できます。

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

投稿には、前と同じ方法でタグを付けることができるようになりました。

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

SaveChanges を呼び出した後に [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を見ると、ペイロードプロパティが適切に設定されていることがわかります。

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a>ペイロード値の明示的な設定

前の例の後に、自動的に生成された値を使用しないペイロードプロパティを追加してみましょう。

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

前と同じように投稿をタグ付けすることができるようになりました。結合エンティティは自動的に作成されます。 このエンティティには、「追跡対象の [エンティティへのアクセス](xref:core/change-tracking/entity-entries)」で説明されているいずれかのメカニズムを使用してアクセスできます。 たとえば、次のコードでは、を使用して <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 結合エンティティインスタンスにアクセスします。

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

結合エンティティが特定されたら、通常の方法で操作できます。この例では、 `TaggedBy` SaveChanges を呼び出す前にペイロードプロパティを設定します。

> [!NOTE]
> ここでは、を呼び出すことによって、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ナビゲーションプロパティの変更を検出し、を使用する前に結合エンティティインスタンスを作成する EF Core を提供する必要があることに注意して `Find` ください。 詳細については [、「変更の検出と通知](xref:core/change-tracking/change-detection) 」を参照してください。

また、結合エンティティを明示的に作成して、投稿をタグに関連付けることもできます。 例:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

最後に、ペイロードデータを設定するもう1つの方法は、 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> データベースを更新する前に、イベントをオーバーライドするか、イベントを使用してエンティティを <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> 処理することです。 次に例を示します。

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]

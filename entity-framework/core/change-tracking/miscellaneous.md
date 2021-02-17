---
title: その他の Change Tracking 機能-EF Core
description: EF Core の変更の追跡に関連するその他の機能とシナリオ
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 9eb3186f4eef300e4824dc86700497444ece4a2c
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543420"
---
# <a name="additional-change-tracking-features"></a>その他の Change Tracking 機能

このドキュメントでは、変更の追跡に関連するその他の機能とシナリオについて説明します。

> [!TIP]
> このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。 これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

> [!TIP]
> このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures)することで実行およびデバッグできます。

## <a name="add-versus-addasync"></a>`Add` と `AddAsync`

Entity Framework Core (EF Core) は、そのメソッドを使用すると、データベースとの対話が発生する可能性がある場合に、非同期メソッドを提供します。 また、高パフォーマンスの非同期アクセスをサポートしていないデータベースを使用する場合のオーバーヘッドを避けるために、同期メソッドも用意されています。

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType>通常はデータベースにアクセスしません。これらのメソッドは、エンティティの追跡を開始するだけであるためです。 ただし、値生成の形式によっては、キー値を生成するためにデータベースにアクセスする _場合があり_ ます。 これを行う唯一の値ジェネレーターは、EF Core に付属してい <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> ます。 このジェネレーターの使用は一般的ではありません。既定では構成されません。 これは、ほとんどのアプリケーションではなく、を使用する必要があることを意味し `Add` `AddAsync` ます。

、、などのその他の同様のメソッド `Update` に `Attach` `Remove` は、新しいキー値を生成しないため、データベースにアクセスする必要がないため、非同期のオーバーロードはありません。

## <a name="addrange-updaterange-attachrange-and-removerange"></a>`AddRange`、`UpdateRange`、`AttachRange`、および `RemoveRange`

<xref:Microsoft.EntityFrameworkCore.DbSet%601> とは <xref:Microsoft.EntityFrameworkCore.DbContext> `Add` 、 `Update` `Attach` `Remove` 1 回の呼び出しで複数のインスタンスを受け入れる、、、およびの代替バージョンを提供します。 これらのメソッドは <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> 、それぞれ、、、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> および <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> です。

これらのメソッドは便利な方法として提供されています。 "Range" メソッドを使用すると、同等の非範囲メソッドを複数回呼び出した場合と同じ機能が得られます。 2つの方法には、パフォーマンスに大きな違いはありません。

> [!NOTE]
> これは EF6 とは異なり、 `AddRange` との `Add` 両方が自動的に呼び出され `DetectChanges` ますが、複数回呼び出すと、検出され `Add` た変更が1回ではなく複数回呼び出される原因になります。 これ `AddRange` は、EF6 でより効率的になりました。 EF Core では、これらのメソッドはどちらも自動的にを呼び出しません `DetectChanges` 。

## <a name="dbcontext-versus-dbset-methods"></a>DbContext と Dbcontext メソッド

、、、などの多くのメソッドには、 `Add` `Update` `Attach` `Remove` との両方に実装があり <xref:Microsoft.EntityFrameworkCore.DbSet%601> <xref:Microsoft.EntityFrameworkCore.DbContext> ます。 これらのメソッドの動作は、通常のエンティティ型 _とまったく同じ_ です。 これは、エンティティの CLR 型が、EF Core モデルの1つのエンティティ型にのみマップされるためです。 したがって、CLR 型は、エンティティがモデルに適合する場所を完全に定義するので、使用する DbSet を暗黙的に決定できます。

この規則の例外は、EF Core 5.0 で導入された共有型のエンティティ型を使用する場合です。これは、主に多対多の結合エンティティに対して行われます。 共有型のエンティティ型を使用する場合は、使用する EF Core モデル型に対して、最初に DbSet を作成する必要があります。 、、、などのメソッドは、使用されている `Add` `Update` `Attach` `Remove` EF Core モデル型を明確にすることなく、dbset で使用できます。

共有型のエンティティ型は、多対多リレーションシップの結合エンティティに対して既定で使用されます。 また、共有型のエンティティ型は、多対多リレーションシップで使用するように明示的に構成することもできます。 たとえば、次のコードは、 `Dictionary<string, int>` 結合エンティティ型としてを構成します。

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

[外部キーとナビゲーションを変更すると](xref:core/change-tracking/relationship-changes) 、新しい結合エンティティインスタンスを追跡することによって、2つのエンティティを関連付ける方法が示されます。 次のコードは、 `Dictionary<string, int>` 結合エンティティに使用される共有型のエンティティ型に対してこれを実行します。

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

<xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType>は、エンティティ型の DbSet を作成するために使用されることに注意 `PostTag` してください。 この DbSet を使用し `Add` て、新しい結合エンティティインスタンスでを呼び出すことができます。

> [!IMPORTANT]
> 規則に従ってエンティティ型を結合するために使用される CLR 型は、パフォーマンスを向上させるために将来のリリースで変更される可能性があります。 上記のコードでのの実行として明示的に構成されている場合を除き、特定の結合エンティティ型に依存しないでください `Dictionary<string, int>` 。

## <a name="property-versus-field-access"></a>プロパティとフィールドのアクセス

EF Core 3.0 以降では、エンティティプロパティへのアクセスには、既定でプロパティのバッキングフィールドが使用されます。 これは効率的であり、プロパティの getter と setter を呼び出すことによる副作用のトリガーを回避します。 たとえば、遅延読み込みによって無限ループがトリガーされるのを防ぐことができます。 モデルでのバッキングフィールドの構成の詳細については、「 [バッキングフィールド](xref:core/modeling/backing-field) 」を参照してください。

場合によっては、プロパティ値を変更するときに、EF Core が副作用を生成することが望ましい場合があります。 たとえば、エンティティにデータをバインドするときに、プロパティを設定すると、U.I. への通知が生成される場合があります。 これは、フィールドを直接設定するときには発生しません。 これは、のを変更することで実現でき <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> ます。

- を使用したモデル内のすべてのエンティティ型 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- を使用した特定のエンティティ型のすべてのプロパティとナビゲーション <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- を使用する特定のプロパティ <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- を使用した特定のナビゲーション <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

プロパティアクセスモード `Field` と `PreferField` により、EF Core は、バッキングフィールドを介してプロパティ値にアクセスします。 同様 `Property` に、と `PreferProperty` は、getter および setter を介して、EF Core がプロパティ値にアクセスすることになります。

`Field`または `Property` が使用されていて、EF Core がフィールドまたはプロパティの getter/setter を使用して値にアクセスできない場合、EF Core は例外をスローします。 これにより、常にフィールドまたはプロパティへのアクセスが使用されていると考えられる場合に、EF Core が使用されます。

一方、 `PreferField` `PreferProperty` 優先アクセスを使用できない場合、モードとモードはそれぞれ、プロパティまたはバッキングフィールドを使用するように切り替えられます。 `PreferField` は EF Core 3.0 以降の既定値です。 つまり、EF Core では、可能な場合は常にフィールドが使用されますが、getter または setter を使用してプロパティにアクセスする必要がある場合は失敗しません。

`FieldDuringConstruction` また、 `PreferFieldDuringConstruction` _エンティティインスタンスの作成時にのみ_ バッキングフィールドを使用するように EF Core を構成します。 これにより、getter と setter の副作用なしでクエリを実行できるようになります。 EF Core によって後で変更されると、これらの副作用が発生します。 `PreferFieldDuringConstruction` は EF Core 3.0 より前の既定値でした。

次の表では、さまざまなプロパティアクセスモードについてまとめています。

| PropertyAccessMode              | 順位 | エンティティ作成の基本設定 | フォールバック | フォールバック (エンティティを作成する)
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | フィールド      | フィールド                        | スロー   | スロー
| `Property`                      | プロパティ   | プロパティ                     | スロー   | スロー
| `PreferField`                   | フィールド      | フィールド                        | プロパティ | プロパティ
| `PreferProperty`                | プロパティ   | プロパティ                     | フィールド    | フィールド
| `FieldDuringConstruction`       | プロパティ   | フィールド                        | フィールド    | スロー
| `PreferFieldDuringConstruction` | プロパティ   | フィールド                        | フィールド    | プロパティ

## <a name="temporary-values"></a>一時的な値

EF Core は、SaveChanges が呼び出されたときにデータベースによって生成される実際のキー値を持つ新しいエンティティを追跡するときに、一時的なキー値を作成します。 これらの一時的な値の使用方法の概要については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

### <a name="accessing-temporary-values"></a>一時的な値へのアクセス

EF Core 3.0 以降では、一時的な値は変更トラッカーに格納され、エンティティインスタンスに直接設定されることはありません。 ただし、これらの一時値は、追跡対象のエンティティに [アクセス](xref:core/change-tracking/entity-entries)するためのさまざまなメカニズムを使用する場合に公開 _され_ ます。 たとえば、次のコードはを使用して一時的な値にアクセスし <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> ます。

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

このコードからの出力は次のとおりです。

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> を使用すると、一時的な値を確認できます。

### <a name="manipulating-temporary-values"></a>一時値の操作

一時的な値を明示的に使用すると便利な場合があります。 たとえば、新しいエンティティのコレクションを web クライアント上に作成し、サーバーにシリアル化して戻すことができます。 外部キーの値は、これらのエンティティ間のリレーションシップを設定するための1つの方法です。 次のコードでは、この方法を使用して、新しいエンティティのグラフを外部キーで関連付け、SaveChanges が呼び出されたときに実際のキー値を生成することができます。

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

次のことに注意してください。

- 負の数値は、一時キーの値として使用されます。これは必須ではありませんが、キーの競合を防ぐための一般的な規則です。
- `Post.BlogId`FK プロパティには、関連付けられているブログの PK と同じ負の値が割り当てられます。
- 各エンティティが追跡された後に、PK 値が一時的としてマークされ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> ます。 これは、アプリケーションによって指定されたキー値が実際のキー値であると見なされるために必要です。

SaveChanges を呼び出す前に [change tracker デバッグビューを確認](xref:core/change-tracking/debug-views) すると、PK 値が一時的としてマークされ、投稿が正しいブログに関連付けられていることがわかります。これには、ナビゲーションの修正が含まれます。

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

を呼び出した後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 、これらの一時値は、データベースによって生成される実際の値に置き換えられています。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>既定値の使用

EF Core を指定すると、プロパティは、の呼び出し時にデータベースから既定値を取得でき <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。 生成されたキー値と同様に、値が明示的に設定されていない場合、EF Core はデータベースからの既定値のみを使用します。 たとえば、次のエンティティ型について考えてみます。

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

`ValidFrom`プロパティは、データベースから既定値を取得するように構成されています。

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

この型のエンティティを挿入すると、明示的な値が設定されていない限り、EF Core によってデータベースで値が生成されます。 次に例を示します。

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

[変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views)を見ると、最初のトークンが `ValidFrom` データベースによって生成され、2番目のトークンが値を明示的に設定したことが示されます。

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> データベースの既定値を使用するには、データベース列に既定値制約が構成されている必要があります。 これは、またはを使用しているときに EF Core 移行によって自動的に行われ <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> ます。 EF Core 移行を使用しない場合は、他の方法で列に default 制約を作成するようにしてください。

### <a name="using-nullable-properties"></a>Null 値を許容するプロパティの使用

EF Core は、プロパティ値をその型の CLR の既定値と比較することによって、プロパティが設定されているかどうかを判断できます。 これはほとんどの場合に適していますが、CLR default をデータベースに明示的に挿入することはできません。 たとえば、次のように整数のプロパティを持つエンティティについて考えてみます。

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

このプロパティは、データベースの既定値-1 を持つように構成されています。

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

その目的は、明示的な値が設定されていない場合に、既定の-1 が使用されることです。 ただし、値を 0 (整数の CLR の既定値) に設定しても、値が設定されていないことを EF Core すると区別できないため、このプロパティに0を挿入することはできません。 次に例を示します。

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

`Count`が明示的に0に設定されているインスタンスは、引き続きデータベースから既定値を取得することに注意してください。これは、意図したものではありません。 これに対処する簡単な方法は、プロパティを null 許容にすることです `Count` 。

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

これにより、CLR の既定値は0ではなく null になります。これは、明示的に設定したときに0が挿入されることを意味します。

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>Null 許容のバッキングフィールドの使用

> [!NOTE]
> この null 許容のバッキングフィールドパターンは EF Core 5.0 以降でサポートされています。

このプロパティを null 許容にすると、ドメインモデルで概念的に null 値が許容されないことに問題があります。 このため、プロパティを強制的に nullable にすると、モデルは侵害されます。

EF Core 5.0 以降では、プロパティを null 非許容にすることができます。これにより、バッキングフィールドだけを null 許容にすることができます。 次に例を示します。

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

これにより、プロパティが明示的に0に設定されている場合に CLR の既定値 (0) を挿入できるようになります。一方、ドメインモデルでは、プロパティを null 許容として公開する必要はありません。 次に例を示します。

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Bool プロパティの null 許容のバッキングフィールド

このパターンは、ブール型のプロパティをストアで生成された既定値と共に使用する場合に特に便利です。 の CLR の既定値 `bool` は "false" であるため、通常のパターンを使用して "false" を明示的に挿入することはできません。 たとえば、エンティティ型について考えてみ `User` ます。

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

`IsAuthorized`プロパティは、データベースの既定値 "true" を使用して構成されます。

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

プロパティは、 `IsAuthorized` 挿入前に明示的に "true" または "false" に設定できます。また、データベースの既定値をそのままにしておくこともできます。

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

SQLite を使用しているときの SaveChanges からの出力は、データベースの既定値が Mac で使用されていることを示していますが、Alice と Baxter には明示的な値が設定されています。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>スキーマの既定値のみ

場合によっては、これらの値を挿入に使用しなくて EF Core も、EF Core の移行によって作成されたデータベーススキーマに既定値を設定すると便利です。 これは、次のようにプロパティを構成することによって実現でき <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> ます。

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]

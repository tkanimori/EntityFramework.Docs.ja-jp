---
title: リレーションシップの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 1732d32643effb0f12111191ed4ba3abb4182d93
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26053032"
---
# <a name="relationships"></a>関係

リレーションシップを定義する方法の 2 つのエンティティ互いに関連します。 リレーショナル データベース、これは、外部キー制約で表されます。

> [!NOTE]  
> この記事のサンプルの大部分では、一対多のリレーションシップを使用して、概念を示します。 一対一および多対多のリレーションシップの例については、[その他のリレーションシップのパターン](#other-relationship-patterns)記事の最後のセクションです。

## <a name="definition-of-terms"></a>用語の定義

リレーションシップの記述に使用される用語の数があります。

* **依存エンティティ:** これは、外部キー プロパティを含むエンティティです。 リレーションシップの 'child' と呼ばれることがあります。

* **プリンシパル エンティティ:** これは、プライマリ/代替のキー プロパティを含むエンティティです。 リレーションシップの 'parent' と呼ばれることがあります。

* **外部キー:** エンティティに関連するプリンシパル キー プロパティの値を格納するために使用される、依存エンティティのプロパティです。

* **プリンシパル キー:** プリンシパル エンティティを一意に識別するプロパティです。 主キーや代替キーがあります。

* **ナビゲーション プロパティ。** 関連会計主体への参照を格納しているプリンシパルや依存するエンティティで定義されたプロパティ。

  * **コレクション ナビゲーション プロパティ。** を多くの関連エンティティへの参照を含むナビゲーション プロパティ。

  * **ナビゲーション プロパティの参照:** 1 つの関連するエンティティへの参照を保持するナビゲーション プロパティ。

  * **逆ナビゲーション プロパティ。** 特定のナビゲーション プロパティを検討する場合、リレーションシップのもう一方の端のナビゲーション プロパティに関係する用語です。

次のコード リストは、一対多関係を示しています`Blog`と。`Post`

* `Post`依存エンティティは、します。

* `Blog`プリンシパル エンティティには

* `Post.BlogId`外部キーです。

* `Blog.BlogId`(この場合は代替キーではなく、主キー)、プリンシパル キーには

* `Post.Blog`参照ナビゲーション プロパティは、します。

* `Blog.Posts`コレクション ナビゲーション プロパティは、します。

* `Post.Blog`逆ナビゲーション プロパティは、 `Blog.Posts` (その逆)

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>規則

規則では、リレーションシップが、型上で検出されたナビゲーション プロパティがある場合に、それが作成されます。 プロパティは、現在のデータベース プロバイダーではスカラー型として指す型がマップされないことができる場合、ナビゲーション プロパティと見なされます。

> [!NOTE]  
> プリンシパル エンティティの主キー ターゲット規則によって検出されたリレーションシップに常にします。 代替キーをターゲットにするには Fluent API を使用して追加の構成を実行する必要があります。

### <a name="fully-defined-relationships"></a>完全に定義されたリレーションシップ

リレーションシップの最も一般的なパターンでは、リレーションシップと依存するエンティティ クラスで定義されている外部キーのプロパティの両端で定義されているナビゲーション プロパティがあります。

* 2 つの型の間のナビゲーション プロパティのペアが見つかった場合が同じリレーションシップの逆ナビゲーション プロパティとして構成されます。

* 依存エンティティには、という名前のプロパティが含まれている場合`<primary key property name>`、 `<navigation property name><primary key property name>`、または`<principal entity name><primary key property name>`外部キーとしてように構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 2 つの型の間で定義されている複数のナビゲーション プロパティがあるかどうか (つまり互いを指すナビゲーションの 1 つ以上の個別ペア) し、リレーションシップは作成されません慣例により、および手動で構成を特定するようにする必要があります、1 組のナビゲーション プロパティ。

### <a name="no-foreign-key-property"></a>外部キー プロパティはありません。

推奨されますが、依存エンティティ クラスで定義されている外部キー プロパティを持つ、必須ではありません。 外部キーのプロパティが見つからない場合シャドウ外部キーのプロパティを名前で導入される予定`<navigation property name><principal key property name>`(を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>単一のナビゲーション プロパティ

1 つのナビゲーション プロパティ (ありません逆のナビゲーションと外部キー プロパティはありません) を含めることは、規約によって定義されたリレーションシップが十分です。 単一のナビゲーション プロパティと外部キーのプロパティもあります。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>連鎖削除

規則では、連鎖削除に設定されます*Cascade*の必要なリレーションシップと*ClientSetNull*省略可能なリレーションシップの場合。 *Cascade*依存エンティティも削除することを意味します。 *ClientSetNull*はメモリに読み込まれていない依存エンティティが維持されることを意味変更せずとする必要があります手動でまたは削除する有効なプリンシパル エンティティを指すように更新します。 メモリに読み込まれているエンティティの EF コアしようと外部キー プロパティを null に設定します。

参照してください、[必須および省略可能なリレーションシップ](#required-and-optional-relationships)必須およびオプションの関係の違いについてのセクションです。

参照してください[連鎖削除](../saving/cascade-delete.md)の詳細については、さまざまな規則が使用する既定の動作とを削除します。

## <a name="data-annotations"></a>データの注釈

、リレーションシップを構成するために使用する 2 つのデータの注釈がある`[ForeignKey]`と`[InverseProperty]`です。

### <a name="foreignkey"></a>[ForeignKey]

データ注釈を使用すると、構成するプロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。 外部キー プロパティは、規則によって検出されていない場合にこれは通常です。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> `[ForeignKey]`リレーションシップのいずれかのナビゲーション プロパティに注釈を配置することができます。 ナビゲーションのプロパティ、依存エンティティ クラスに移動する必要はありません。

### <a name="inverseproperty"></a>[InverseProperty]

データ注釈を使用して、依存とプリンシパル エンティティのナビゲーション プロパティのペアを構成することができます。 2 つのエンティティ型の間のナビゲーション プロパティの 1 つ以上のペアがある場合にこれは通常です。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a>Fluent API

リレーションシップ Fluent api を構成するのには、リレーションシップを構成するナビゲーション プロパティを識別することによって開始します。 `HasOne`または`HasMany`の構成を開始するエンティティ型にナビゲーション プロパティを識別します。 呼び出しをチェーンする`WithOne`または`WithMany`を逆のナビゲーションを識別します。 `HasOne`/`WithOne`参照ナビゲーション プロパティが使用されると`HasMany` / `WithMany`コレクション ナビゲーション プロパティに対して使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a>単一のナビゲーション プロパティ

1 つのナビゲーション プロパティのみがあるかどうかは、パラメーターなしのオーバー ロードの`WithOne`と`WithMany`です。 これは、参照またはコレクションがリレーションシップのもう一方の端に概念的にが、エンティティ クラスに含まれるナビゲーション プロパティが存在しないことを示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a>外部キー

Fluent API を使用すると、構成するプロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

次のコード リストは、複合外部キーを構成する方法を示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

文字列のオーバー ロードを使用する`HasForeignKey(...)`の外部キーとしてシャドウ プロパティの構成 (を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。 以下の手順に従って)、外部キーとして使用する前に、モデルに明示的にシャドウ プロパティを追加することをお勧めします。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a>プリンシパル キー

主キー以外のプロパティを参照する外部キーにする場合は、Fluent API を使用して、リレーションシップのプリンシパル キー プロパティを構成することができます。 キーは、プリンシパルとして自動的に構成されているプロパティを代替キーとしてセットアップする (を参照してください[代替キー](alternate-keys.md)詳細については)。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/PrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => s.CarLicensePlate)
            .HasPrincipalKey(c => c.LicensePlate);
    }
}

public class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

次のコード リストは、複合主キーを構成する方法を示します。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => new { s.CarState, s.CarLicensePlate })
            .HasPrincipalKey(c => new { c.State, c.LicensePlate });
    }
}

public class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarState { get; set; }
    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

> [!WARNING]  
> プリンシパル キー プロパティを指定した順序は、外部キーの指定された順序と一致する必要があります。

### <a name="required-and-optional-relationships"></a>必須およびオプションの関係

Fluent API を使用して、リレーションシップが必須またはオプションであるかどうかを構成することができます。 最終的に外部キー プロパティが必須またはオプションかどうかを制御します。 これは、機能は、シャドウ状態の外部キーを使用しているときに最も便利です。 外部キーのプロパティがエンティティ クラスであるかどうかは、リレーションシップの requiredness は外部キー プロパティが必須またはオプションかどうかに基づいて決定されます (を参照してください[必須および省略可能なプロパティ](required-optional.md)の詳細情報)。

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/Required.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

### <a name="cascade-delete"></a>連鎖削除

Fluent API を使用すると、特定のリレーションシップの連鎖削除の動作を明示的に構成します。

参照してください[連鎖削除](../saving/cascade-delete.md)各オプションの詳細については、データの保存セクションでします。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CascadeDelete.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="other-relationship-patterns"></a>その他のリレーションシップのパターン

### <a name="one-to-one"></a>一対一

一対一のリレーションシップでは、両方の側で参照ナビゲーション プロパティがあります。 一対多リレーションシップと同じ規則に従いますが、各プリンシパルに関連する 1 つだけの依存することを確認する外部キー プロパティに一意のインデックスが導入されました。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/Relationships/OneToOne.cs?highlight=6,15,16)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

> [!NOTE]  
> EF は外部キーのプロパティを検出するには、その機能に基づく依存するエンティティのいずれかを選択します。 従属変数として間違ったエンティティを選択した場合は、これを修正する Fluent API を使用できます。

使用するリレーションシップ Fluent api を構成する場合、`HasOne`と`WithOne`メソッドです。

-依存エンティティの種類を指定する必要があります。 外部キーを構成するときに提供されるジェネリック パラメーターに注意してください`HasForeignKey`の下の一覧にします。 一対多リレーションシップでは、オフにしている参照ナビゲーションを持つエンティティが依存するコレクションを使用して 1 つは、プリンシパルです。 はありませんが、一対一リレーションシップのためにを明示的に定義する必要があります。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/OneToOne.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<BlogImage> BlogImages { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasOne(p => p.BlogImage)
            .WithOne(i => i.Blog)
            .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogForeignKey { get; set; }
    public Blog Blog { get; set; }
}
```

### <a name="many-to-many"></a>[多対多]

結合テーブルを表すエンティティ クラスを持たない多対多リレーションシップはまだサポートされていません。 ただし、結合テーブルとのマッピング 2 つの独立した一対多リレーションシップのエンティティ クラスを含めることによって、多対多リレーションシップを表すことができます。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(t => new { t.PostId, t.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```

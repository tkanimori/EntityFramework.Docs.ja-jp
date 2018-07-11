---
title: リレーションシップ - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 0e60ade605ffb73b02934ea32f1c757affce970d
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949219"
---
# <a name="relationships"></a>リレーションシップ

2 つのエンティティを定義するリレーションシップを互いに関連します。 リレーショナル データベースで外部キー制約によって表されます。

> [!NOTE]  
> この記事のサンプルのほとんどは、概念を説明するのに一対多リレーションシップを使用します。 一対一および多対多のリレーションシップの例については、[リレーションシップの他のパターン](#other-relationship-patterns)記事の最後のセクション。

## <a name="definition-of-terms"></a>用語の定義

リレーションシップの記述に使用される用語のいくつか

* **依存エンティティ:** これは、外部キー プロパティを含むエンティティ。 リレーションシップの「子」とも呼ばれます。

* **プリンシパル エンティティ:** これは、プライマリ/代替キー プロパティを含むエンティティ。 リレーションシップの「親」と呼ばれるにあります。

* **外部キー:** エンティティに関連するプリンシパルのキー プロパティの値の格納に使用される依存エンティティのプロパティ。

* **プリンシパルのキー:** プリンシパル エンティティを一意に識別するプロパティ。 主キーや代替キーがあります。

* **ナビゲーション プロパティ:** 関連会計主体にへの参照を格納しているプリンシパルや依存エンティティで定義されたプロパティ。

  * **コレクション ナビゲーション プロパティ:** 多くの関連エンティティへの参照を含むナビゲーション プロパティ。

  * **参照ナビゲーション プロパティ:** 1 つの関連エンティティへの参照を保持するナビゲーション プロパティ。

  * **逆のナビゲーション プロパティ:** 特定のナビゲーション プロパティを説明しながら、リレーションシップの他方の end のナビゲーション プロパティに関係する用語です。

次のコード リストは、一対多の関係を示しています`Blog`と。 `Post`

* `Post` 依存エンティティは、します。

* `Blog` プリンシパル エンティティには

* `Post.BlogId` 外部キーです。

* `Blog.BlogId` プリンシパルのキー (この例では、代替キーではなく、主キー)

* `Post.Blog` 参照ナビゲーション プロパティは、します。

* `Blog.Posts` コレクション ナビゲーション プロパティは、します。

* `Post.Blog` 逆のナビゲーション プロパティは、 `Blog.Posts` (その逆)

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>規約

慣例によりは、型上で検出されたナビゲーション プロパティがある場合に、リレーションシップが作成されます。 現在のデータベース プロバイダーによってスカラー型として指す型がマップしないことができる場合、プロパティはナビゲーション プロパティと見なされます。

> [!NOTE]  
> 規則によって検出されたリレーションシップは、プリンシパル エンティティの主キーを常に対象とします。 代替キーを対象とは、Fluent API を使用して追加の構成を実行する必要があります。

### <a name="fully-defined-relationships"></a>完全に定義されたリレーションシップ

リレーションシップの最も一般的なパターンでは、リレーションシップと依存エンティティ クラスで定義されている外部キー プロパティの両方の end で定義されているナビゲーション プロパティがあります。

* 2 つの型の間のナビゲーション プロパティのペアが見つかった場合同じリレーションシップの逆のナビゲーション プロパティとして構成されます。

* 依存エンティティには、という名前のプロパティが含まれている場合`<primary key property name>`、 `<navigation property name><primary key property name>`、または`<principal entity name><primary key property name>`し、外部キーとして構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 2 つの型の間で定義されている複数のナビゲーション プロパティがあるかどうか (つまり、互いを指すナビゲーションの 1 つ以上の個別のペア)、し、リレーションシップは作成されません慣例および識別するためにそれらを手動で構成する必要がある方法ナビゲーション プロパティを組み合わせます。

### <a name="no-foreign-key-property"></a>外部キー プロパティ

依存エンティティ クラスで定義されている外部キー プロパティを持つことをお勧めしますが、これは必要ありません。 名前でシャドウの外部キー プロパティが導入される外部キー プロパティが見つからない場合`<navigation property name><principal key property name>`(を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>単一のナビゲーション プロパティ

(ありません逆のナビゲーションと外部キー プロパティ) の 1 つだけのナビゲーション プロパティを含めると、規則で定義されたリレーションシップを持つだけです。 単一のナビゲーション プロパティと外部キー プロパティすることもできます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>連鎖削除

設定される連鎖削除規則により、 *Cascade*に必要なリレーションシップと*ClientSetNull*の省略可能なリレーションシップです。 *Cascade*依存エンティティも削除されることを意味します。 *ClientSetNull*メモリに読み込まれていない依存するエンティティが存在することを意味変更なしとする必要があります手動で削除、または有効なプリンシパル エンティティをポイントするように更新します。 メモリに読み込まれるエンティティの場合は、EF Core を外部キー プロパティを null に設定を試みます。

参照してください、[必須および省略可能リレーションシップ](#required-and-optional-relationships)必須および省略可能なリレーションシップの違い」セクション。

参照してください[連鎖削除](../saving/cascade-delete.md)の詳細について、さまざまな動作と規約によって使用される既定値を削除しています。

## <a name="data-annotations"></a>データの注釈

リレーションシップを構成するのに使用できる 2 つのデータ注釈がある`[ForeignKey]`と`[InverseProperty]`します。

### <a name="foreignkey"></a>[不変]

データ注釈を使用すると、構成プロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。 外部キー プロパティが規則によって検出されないときにこれは通常です。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> `[ForeignKey]`リレーションシップのいずれかのナビゲーション プロパティに注釈を配置できます。 依存エンティティ クラスでナビゲーション プロパティに移動する必要はありません。

### <a name="inverseproperty"></a>[InverseProperty]

依存とプリンシパル エンティティのナビゲーション プロパティを組み合わせる方法を構成するのには、データ注釈を使用できます。 2 つのエンティティ型の間のナビゲーション プロパティの 1 つ以上のペアがある場合にこれは通常です。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a>Fluent API

Fluent API でのリレーションシップを構成するには、まず、リレーションシップを作成するナビゲーション プロパティを識別します。 `HasOne` または`HasMany`の構成を開始するエンティティ型にナビゲーション プロパティを識別します。 呼び出しをチェーンする`WithOne`または`WithMany`逆ナビゲーションを識別するためにします。 `HasOne`/`WithOne` 参照ナビゲーション プロパティに対して使用し、 `HasMany` / `WithMany`コレクション ナビゲーション プロパティに対して使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a>単一のナビゲーション プロパティ

1 つのナビゲーション プロパティのみがある場合のパラメーターなしのオーバー ロードがあります`WithOne`と`WithMany`します。 これは、参照またはコレクションがリレーションシップのもう一方の端に概念的にが、エンティティ クラスに含まれるナビゲーション プロパティがないことを示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a>外部キー

Fluent API を使用すると、構成プロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

次のコード リストは、複合外部キーを構成する方法を示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

文字列のオーバー ロードを使用する`HasForeignKey(...)`外部キーとしてシャドウ プロパティを構成する (を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。 (下図参照)、外部キーとして使用する前に、モデルを明示的にシャドウ プロパティを追加することをお勧めします。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a>プリンシパル キー

主キー以外のプロパティを参照する外部キーを設定する場合は、リレーションシップのプリンシパルのキー プロパティを構成する Fluent API を使用できます。 プリンシパルは、キーとして自動的に構成されているプロパティである代替キーとしてのセットアップ (を参照してください[代替キー](alternate-keys.md)詳細については)。

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
> プリンシパルのキー プロパティを指定した順序は、外部キーの指定された順序と一致する必要があります。

### <a name="required-and-optional-relationships"></a>必須およびオプションの関係

Fluent API を使用して、リレーションシップが必須か省略可能かどうかを構成することができます。 最終的に外部キー プロパティが必須または省略可能なのかどうかを制御します。 これは、シャドウ状態の外部キーを使用しているときに最も役立ちます。 外部キー プロパティがエンティティ クラスであるかどうかは、リレーションシップの requiredness が外部キー プロパティが必須または省略可能なのかどうかに基づいて決定されます (を参照してください[必須および省略可能なプロパティ](required-optional.md)詳細情報)。

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

Fluent API を使用して、明示的に指定したリレーションシップの連鎖削除の動作を構成することができます。

参照してください[連鎖削除](../saving/cascade-delete.md)各オプションの詳細についてはデータの保存 セクションでします。

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

一対一のリレーションシップでは、両方の側での参照ナビゲーション プロパティがあります。 一対多のリレーションシップと同じ規則に従ってがプリンシパルごとに 1 つだけの依存が関連することを確認する場合は、外部キー プロパティに一意のインデックスが導入されています。

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
> EF はベースの外部キー プロパティを検出する機能に依存するエンティティのいずれかを選択します。 依存として間違ったエンティティを選択した場合は、これを修正する Fluent API を使用できます。

使用する Fluent API を使用した、リレーションシップを構成するときに、`HasOne`と`WithOne`メソッド。

-依存エンティティ型を指定する必要がある外部キーを構成するときに提供されるジェネリック パラメーターに注意してください`HasForeignKey`で以下のリスト。 一対多リレーションシップでは、参照ナビゲーションを使用したエンティティは、の依存しており、コレクションを使用して 1 つは、プリンシパルには明らかです。 はありませんが、一対一リレーションシップのためを明示的に定義する必要があります。

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

### <a name="many-to-many"></a>多対多

結合テーブルを表すエンティティ クラスを持たない多対多リレーションシップはまだサポートされていません。 ただし、結合テーブルとのマッピングで独立した 2 つの一対多リレーションシップのエンティティ クラスを含めることによって、多対多リレーションシップを表すことができます。

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

---
title: 継承 (リレーショナル データベース) の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
ms.technology: entity-framework-core
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 22eed0002b5903d3cfd18a7e4af0fcd2d46a5c4c
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2018
ms.locfileid: "29152364"
---
# <a name="inheritance-relational-database"></a>継承 (リレーショナル データベース)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

EF モデルでの継承を使用すると、データベース内のエンティティ クラスの継承の表現方法を制御します。

> [!NOTE]  
> 現時点では、テーブルの階層あたり (TPH) パターンのみが、EF コアに実装されます。 テーブルあたり型 (TPT) などの他の一般的なパターンと、テーブルあたりの具象型のタイプ (TPC) はまだ使用できません。

## <a name="conventions"></a>規約

規則では、継承をテーブルあたり階層 (TPH) パターンを使用してマップされます。 TPH では、1 つのテーブルを使用して、階層内のすべての種類のデータを格納します。 識別子の列は、各行が表すどの種類の識別に使用します。

2 つまたは複数の継承された型がモデルで明示的に含まれている場合は、EF コアに継承はセットアップだけ (を参照してください[継承](../inheritance.md)詳細)。

以下には、単純な継承シナリオと TPH パターンを使用してリレーショナル データベースのテーブルに格納されたデータを示す例を示します。 *識別子*列の種類を識別する*ブログ*は行ごとに格納します。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/Samples/InheritanceDbSets.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<RssBlog> RssBlogs { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

![イメージ](_static/inheritance-tph-data.png)

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、名前と識別子の列と、階層内の各型の識別に使用される値の型を構成することができます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("blog_type")
            .HasValue<Blog>("blog_base")
            .HasValue<RssBlog>("blog_rss");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

## <a name="configuring-the-discriminator-property"></a>識別子のプロパティを構成します。

上記の例として、識別子を作成、[プロパティをシャドウ](xref:core/modeling/shadow-properties)の階層の基本エンティティです。 モデル内のプロパティがあるために、その他のプロパティと同じように構成できます。 たとえば、識別子の規則によって、既定値が使用されている場合は、最大の長さを設定するには、します。

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

識別子は、エンティティ内の実際の CLR プロパティにマップできます。 例:
```C#
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("BlogType");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public string BlogType { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

これら 2 つの処理を組み合わせて可能であれば、識別子を実際のプロパティにマップし、構成の両方に。
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

---
title: 継承 (リレーショナル データベース) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 2d0a2abc554f5f115479f886ca3f9f4f01b80b5b
ms.sourcegitcommit: ea1cdec0b982b922a59b9d9301d3ed2b94baca0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66452281"
---
# <a name="inheritance-relational-database"></a>継承 (リレーショナル データベース)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

EF モデルでの継承は、データベース内でエンティティ クラスの継承を表現する方法を制御するために使用されます。

> [!NOTE]  
> 現時点では、EF Core での table-per-hierarchy (TPH) パターンのみが実装されます。 テーブルあたり型 (TPT) などの他の一般的なパターンとテーブル-、具象型の種類 (TPC) はまだ提供されません。

## <a name="conventions"></a>規約

慣例により、継承を-table-per-hierarchy (TPH) パターンを使用してマップされます。 TPH は、階層内のすべての種類のデータを格納するのに 1 つのテーブルを使用します。 識別子列を使用して、各行を表す種類を識別します。

2 つまたは複数の継承された型がモデルで明示的に含まれている場合は、EF Core に継承はセットアップのみ (を参照してください[継承](../inheritance.md)の詳細)。

単純な継承シナリオおよび TPH パターンを使用してリレーショナル データベース テーブルに格納されているデータの例を次に示します。 *識別子*列の型を指定する*ブログ*行ごとに格納されます。

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

>[!NOTE]
> データベースの列が TPH マッピングを使用する場合に自動的に必要に応じて、null 許容設定します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、名前と識別子列と、階層内の各型を識別するために使用される値の型を構成することができます。

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

上記の例では、識別子として作成されます、[プロパティをシャドウ](xref:core/modeling/shadow-properties)階層の基本エンティティです。 モデルのプロパティであるために、その他のプロパティと同じように構成できます。 たとえば、識別子の規則によって、既定値が使用されているときに、最大長を設定します。

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

識別子は、エンティティ内の実際の CLR プロパティにもマップできます。 例えば:
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

これら 2 つを組み合わせることは、識別子を実際のプロパティにマップし、構成の両方にできます。
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

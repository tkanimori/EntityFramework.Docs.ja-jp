---
title: 継承 (リレーショナルデータベース)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: c660107619470a726fe13ad8eee2850749e6dcd9
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812083"
---
# <a name="inheritance-relational-database"></a>継承 (リレーショナル データベース)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。

> [!NOTE]  
> 現時点では、EF Core で実装されているのは、階層単位 (TPH) パターンだけです。 テーブルごとのテーブル (TPT) や具象型ごとのテーブル (TPC) などのその他の一般的なパターンは、まだ使用できません。

## <a name="conventions"></a>規約

慣例により、継承は、階層単位 (TPH) のパターンを使用してマップされます。 TPH は、1つのテーブルを使用して、階層内のすべての型のデータを格納します。 識別子列は、各行が表す型を識別するために使用されます。

EF Core は、継承された複数の型がモデルに明示的に含まれている場合にのみ継承を設定します (詳細については、「[継承](../inheritance.md)」を参照してください)。

単純な継承シナリオと、TPH パターンを使用してリレーショナルデータベーステーブルに格納されているデータを示す例を次に示します。 *識別子*列には、各行に格納されている*ブログ*の種類が示されます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/InheritanceDbSets.cs)] -->
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
> TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、識別子列の名前と型、および階層内の各型を識別するために使用される値を構成できます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
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

## <a name="configuring-the-discriminator-property"></a>識別子プロパティの構成

上の例では、識別子は、階層の基本エンティティの[shadow プロパティ](xref:core/modeling/shadow-properties)として作成されます。 モデルのプロパティであるため、他のプロパティと同様に構成できます。 たとえば、既定の規則に従った識別子を使用する場合の最大長を設定するには、次のようにします。

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

識別子は、エンティティ内の実際の CLR プロパティにマップすることもできます。 (例:

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

これらの2つの要素を組み合わせて、識別子を実際のプロパティにマップして構成することができます。

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

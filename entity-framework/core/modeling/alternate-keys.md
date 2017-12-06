---
title: "代替キー - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
ms.technology: entity-framework-core
uid: core/modeling/alternate-keys
ms.openlocfilehash: 09f86a8932b71ec8f30ee90a088091a00233c20f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="alternate-keys"></a>代替キー

代替キーは、主キーだけでなくエンティティ インスタンスごとに、代替の一意の識別子として機能します。 代替キーは、リレーションシップのターゲットとして使用できます。 リレーショナル データベースを使用するときに代替のキー列と 1 つまたは複数外部キー制約、列を参照する一意のインデックス/制約の概念にマッピングします。

> [!TIP]  
> 代替キーではなく、一意のインデックスの場合、列の一意性を適用する場合は、「[インデックス](indexes.md)です。 EF には、代替キーは、外部キーの対象として使用されることがあるために、一意のインデックスよりも大きい機能を提供します。

代替キーが通常導入されています必要なときに、これらを手動で構成する必要はありません。 参照してください[規則](#conventions)詳細についてはします。

## <a name="conventions"></a>規則

慣例により、リレーションシップのターゲットとして、主キーではないプロパティを識別する場合の代替キーが導入されました。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/AlternateKey.cs?highlight=12)] -->
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
            .HasForeignKey(p => p.BlogUrl)
            .HasPrincipalKey(b => b.Url);
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

    public string BlogUrl { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a>データの注釈

データ注釈を使用する代替キーを構成できないことができます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、代替キーとして使用する 1 つのプロパティを構成することができます。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => c.LicensePlate);
    }
}

class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```

代替キー (複合代替キーと呼ばれます) である複数のプロパティを構成するのに Fluent API を使用することもできます。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```

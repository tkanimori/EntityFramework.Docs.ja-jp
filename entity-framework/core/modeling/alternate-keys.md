---
title: 代替キー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: b26d8bc1630af9e811d9c4e7da850a618bc8042e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996972"
---
# <a name="alternate-keys"></a>代替キー

代替キーは、主キーだけでなくエンティティのインスタンスごとに、代替の一意の識別子として機能します。 代替キーは、リレーションシップのターゲットとして使用できます。 リレーショナル データベースを使用する場合に代替のキーの列と 1 つまたは複数外部キー制約、列を参照する一意のインデックス/制約の概念にマッピングします。

> [!TIP]  
> 代替キーではなく、一意のインデックスが必要し、列の一意性を適用する場合は、「[インデックス](indexes.md)します。 Ef には、外部キーのターゲットとして使用されることがあるために、代替キーは一意のインデックスよりもより多くの機能を提供します。

通常、この代替キーが必要なときに導入されています。 および、それらを手動で構成する必要はありません。 参照してください[規則](#conventions)の詳細。

## <a name="conventions"></a>規約

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

代替キーは、データ注釈を使用していない構成できます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、代替キーを 1 つのプロパティを構成することができます。

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

(別の複合キーと呼ばれます) は代替キーの複数のプロパティを構成するのに Fluent API を使用することもできます。

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

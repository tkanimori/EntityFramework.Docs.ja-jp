---
title: 外部キー制約の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
ms.technology: entity-framework-core
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 726f03e2ee4cd3ec851c9a861b75dd12f9203e9c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052742"
---
# <a name="foreign-key-constraints"></a>外部キー制約

> [!NOTE]  
> このセクションの構成は、一般にリレーショナル データベースに適用されます。 ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。

モデル内の各リレーションシップの外部キー制約が導入されています。

## <a name="conventions"></a>規則

慣例により、外部キー制約が名前付き`FK_<dependent type name>_<principal type name>_<foreign key property name>`します。 複合外部キーの`<foreign key property name>`の外部キー プロパティ名、アンダー スコアの区切られたリストになります。

## <a name="data-annotations"></a>データの注釈

外部キー制約名は、データ注釈を使用して構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、リレーションシップの外部キー制約名を構成することができます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/RelationshipConstraintName.cs?highlight=12)] -->
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
            .HasForeignKey(p => p.BlogId)
            .HasConstraintName("ForeignKey_Post_Blog");
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

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

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
# <a name="foreign-key-constraints"></a><span data-ttu-id="b584a-102">外部キー制約</span><span class="sxs-lookup"><span data-stu-id="b584a-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="b584a-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b584a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="b584a-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="b584a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="b584a-105">モデル内の各リレーションシップの外部キー制約が導入されています。</span><span class="sxs-lookup"><span data-stu-id="b584a-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="b584a-106">規則</span><span class="sxs-lookup"><span data-stu-id="b584a-106">Conventions</span></span>

<span data-ttu-id="b584a-107">慣例により、外部キー制約が名前付き`FK_<dependent type name>_<principal type name>_<foreign key property name>`します。</span><span class="sxs-lookup"><span data-stu-id="b584a-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="b584a-108">複合外部キーの`<foreign key property name>`の外部キー プロパティ名、アンダー スコアの区切られたリストになります。</span><span class="sxs-lookup"><span data-stu-id="b584a-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="b584a-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b584a-109">Data Annotations</span></span>

<span data-ttu-id="b584a-110">外部キー制約名は、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="b584a-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="b584a-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b584a-111">Fluent API</span></span>

<span data-ttu-id="b584a-112">Fluent API を使用して、リレーションシップの外部キー制約名を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="b584a-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

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

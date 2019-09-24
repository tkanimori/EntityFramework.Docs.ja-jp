---
title: Foreign Key 制約-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: d7ed4466f4df9ec01267b048ba1bbcc6e8bbdad5
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197063"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="40370-102">外部キー制約</span><span class="sxs-lookup"><span data-stu-id="40370-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="40370-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="40370-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="40370-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="40370-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="40370-105">外部キー制約は、モデル内のリレーションシップごとに導入されます。</span><span class="sxs-lookup"><span data-stu-id="40370-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="40370-106">規約</span><span class="sxs-lookup"><span data-stu-id="40370-106">Conventions</span></span>

<span data-ttu-id="40370-107">規則により、外部キー制約に`FK_<dependent type name>_<principal type name>_<foreign key property name>`はという名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="40370-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="40370-108">複合外部キー `<foreign key property name>`の場合は、外部キープロパティ名のアンダースコアで区切られたリストになります。</span><span class="sxs-lookup"><span data-stu-id="40370-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="40370-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="40370-109">Data Annotations</span></span>

<span data-ttu-id="40370-110">外部キー制約の名前は、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="40370-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="40370-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="40370-111">Fluent API</span></span>

<span data-ttu-id="40370-112">Fluent API を使用して、リレーションシップの外部キー制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="40370-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?highlight=12)] -->
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

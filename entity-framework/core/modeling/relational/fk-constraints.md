---
title: 外部キー制約 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: a83f72b5d832e349fb4a5fb3b2de0b82bd79ef2a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993989"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="f1db1-102">外部キー制約</span><span class="sxs-lookup"><span data-stu-id="f1db1-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="f1db1-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1db1-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="f1db1-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f1db1-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="f1db1-105">モデル内の各リレーションシップの外部キー制約が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1db1-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="f1db1-106">規約</span><span class="sxs-lookup"><span data-stu-id="f1db1-106">Conventions</span></span>

<span data-ttu-id="f1db1-107">慣例により、外部キー制約の名前は`FK_<dependent type name>_<principal type name>_<foreign key property name>`します。</span><span class="sxs-lookup"><span data-stu-id="f1db1-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="f1db1-108">複合外部キーの`<foreign key property name>`は外部キー プロパティ名、アンダー スコア区切りリストになります。</span><span class="sxs-lookup"><span data-stu-id="f1db1-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="f1db1-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="f1db1-109">Data Annotations</span></span>

<span data-ttu-id="f1db1-110">データ注釈を使用して外部キー制約名を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="f1db1-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f1db1-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f1db1-111">Fluent API</span></span>

<span data-ttu-id="f1db1-112">Fluent API を使用して、リレーションシップの外部キー制約名を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f1db1-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

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

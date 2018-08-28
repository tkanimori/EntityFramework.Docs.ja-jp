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
# <a name="alternate-keys"></a><span data-ttu-id="1c82e-102">代替キー</span><span class="sxs-lookup"><span data-stu-id="1c82e-102">Alternate Keys</span></span>

<span data-ttu-id="1c82e-103">代替キーは、主キーだけでなくエンティティのインスタンスごとに、代替の一意の識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="1c82e-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="1c82e-104">代替キーは、リレーションシップのターゲットとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="1c82e-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="1c82e-105">リレーショナル データベースを使用する場合に代替のキーの列と 1 つまたは複数外部キー制約、列を参照する一意のインデックス/制約の概念にマッピングします。</span><span class="sxs-lookup"><span data-stu-id="1c82e-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="1c82e-106">代替キーではなく、一意のインデックスが必要し、列の一意性を適用する場合は、「[インデックス](indexes.md)します。</span><span class="sxs-lookup"><span data-stu-id="1c82e-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="1c82e-107">Ef には、外部キーのターゲットとして使用されることがあるために、代替キーは一意のインデックスよりもより多くの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="1c82e-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="1c82e-108">通常、この代替キーが必要なときに導入されています。 および、それらを手動で構成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1c82e-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="1c82e-109">参照してください[規則](#conventions)の詳細。</span><span class="sxs-lookup"><span data-stu-id="1c82e-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="1c82e-110">規約</span><span class="sxs-lookup"><span data-stu-id="1c82e-110">Conventions</span></span>

<span data-ttu-id="1c82e-111">慣例により、リレーションシップのターゲットとして、主キーではないプロパティを識別する場合の代替キーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="1c82e-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="1c82e-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="1c82e-112">Data Annotations</span></span>

<span data-ttu-id="1c82e-113">代替キーは、データ注釈を使用していない構成できます。</span><span class="sxs-lookup"><span data-stu-id="1c82e-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="1c82e-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1c82e-114">Fluent API</span></span>

<span data-ttu-id="1c82e-115">Fluent API を使用して、代替キーを 1 つのプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1c82e-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

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

<span data-ttu-id="1c82e-116">(別の複合キーと呼ばれます) は代替キーの複数のプロパティを構成するのに Fluent API を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="1c82e-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

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

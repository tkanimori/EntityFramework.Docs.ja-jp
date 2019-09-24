---
title: 代替キー-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: 87df5d174a1db12fb3ab763ac76c3b863a83087e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197463"
---
# <a name="alternate-keys"></a><span data-ttu-id="45dea-102">代替キー</span><span class="sxs-lookup"><span data-stu-id="45dea-102">Alternate Keys</span></span>

<span data-ttu-id="45dea-103">代替キーは、主キーに加えて、各エンティティインスタンスの代替一意識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="45dea-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="45dea-104">代替キーは、リレーションシップのターゲットとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="45dea-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="45dea-105">リレーショナルデータベースを使用する場合、これは代替キー列の一意のインデックス/制約の概念と、その列を参照する1つ以上の foreign key 制約にマップされます。</span><span class="sxs-lookup"><span data-stu-id="45dea-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="45dea-106">列の一意性を確保するだけの場合は、別のキーではなく一意のインデックスを作成するには、「[インデックス](indexes.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45dea-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="45dea-107">EF では、代替キーは外部キーのターゲットとして使用できるため、一意のインデックスよりも多くの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="45dea-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="45dea-108">通常、代替キーは必要に応じて導入され、手動で構成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="45dea-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="45dea-109">詳細については、「[規約](#conventions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="45dea-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="45dea-110">規約</span><span class="sxs-lookup"><span data-stu-id="45dea-110">Conventions</span></span>

<span data-ttu-id="45dea-111">慣例により、主キーではないプロパティをリレーションシップのターゲットとして指定すると、代替キーが導入されます。</span><span class="sxs-lookup"><span data-stu-id="45dea-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/AlternateKey.cs?highlight=12)] -->
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

## <a name="data-annotations"></a><span data-ttu-id="45dea-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="45dea-112">Data Annotations</span></span>

<span data-ttu-id="45dea-113">データ注釈を使用して代替キーを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="45dea-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="45dea-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="45dea-114">Fluent API</span></span>

<span data-ttu-id="45dea-115">Fluent API を使用して、1つのプロパティを代替キーとして構成することができます。</span><span class="sxs-lookup"><span data-stu-id="45dea-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?highlight=7,8)] -->
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

<span data-ttu-id="45dea-116">また、Fluent API を使用して、複数のプロパティを別のキー (複合代替キーと呼ばれます) として構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="45dea-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?highlight=7,8)] -->
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

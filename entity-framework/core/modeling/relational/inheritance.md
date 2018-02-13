---
title: "継承 (リレーショナル データベース) の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
ms.technology: entity-framework-core
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 22eed0002b5903d3cfd18a7e4af0fcd2d46a5c4c
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2018
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="7f799-102">継承 (リレーショナル データベース)</span><span class="sxs-lookup"><span data-stu-id="7f799-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="7f799-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7f799-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="7f799-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7f799-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="7f799-105">EF モデルでの継承を使用すると、データベース内のエンティティ クラスの継承の表現方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="7f799-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="7f799-106">現時点では、テーブルの階層あたり (TPH) パターンのみが、EF コアに実装されます。</span><span class="sxs-lookup"><span data-stu-id="7f799-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="7f799-107">テーブルあたり型 (TPT) などの他の一般的なパターンと、テーブルあたりの具象型のタイプ (TPC) はまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="7f799-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="7f799-108">規約</span><span class="sxs-lookup"><span data-stu-id="7f799-108">Conventions</span></span>

<span data-ttu-id="7f799-109">規則では、継承をテーブルあたり階層 (TPH) パターンを使用してマップされます。</span><span class="sxs-lookup"><span data-stu-id="7f799-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="7f799-110">TPH では、1 つのテーブルを使用して、階層内のすべての種類のデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="7f799-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="7f799-111">識別子の列は、各行が表すどの種類の識別に使用します。</span><span class="sxs-lookup"><span data-stu-id="7f799-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="7f799-112">2 つまたは複数の継承された型がモデルで明示的に含まれている場合は、EF コアに継承はセットアップだけ (を参照してください[継承](../inheritance.md)詳細)。</span><span class="sxs-lookup"><span data-stu-id="7f799-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="7f799-113">以下には、単純な継承シナリオと TPH パターンを使用してリレーショナル データベースのテーブルに格納されたデータを示す例を示します。</span><span class="sxs-lookup"><span data-stu-id="7f799-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="7f799-114">*識別子*列の種類を識別する*ブログ*は行ごとに格納します。</span><span class="sxs-lookup"><span data-stu-id="7f799-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="7f799-116">データの注釈</span><span class="sxs-lookup"><span data-stu-id="7f799-116">Data Annotations</span></span>

<span data-ttu-id="7f799-117">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="7f799-117">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="7f799-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7f799-118">Fluent API</span></span>

<span data-ttu-id="7f799-119">Fluent API を使用して、名前と識別子の列と、階層内の各型の識別に使用される値の型を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7f799-119">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="7f799-120">識別子のプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="7f799-120">Configuring the discriminator property</span></span>

<span data-ttu-id="7f799-121">上記の例として、識別子を作成、[プロパティをシャドウ](xref:core/modeling/shadow-properties)の階層の基本エンティティです。</span><span class="sxs-lookup"><span data-stu-id="7f799-121">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="7f799-122">モデル内のプロパティがあるために、その他のプロパティと同じように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7f799-122">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="7f799-123">たとえば、識別子の規則によって、既定値が使用されている場合は、最大の長さを設定するには、します。</span><span class="sxs-lookup"><span data-stu-id="7f799-123">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="7f799-124">識別子は、エンティティ内の実際の CLR プロパティにマップできます。</span><span class="sxs-lookup"><span data-stu-id="7f799-124">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="7f799-125">例:</span><span class="sxs-lookup"><span data-stu-id="7f799-125">For example:</span></span>
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

<span data-ttu-id="7f799-126">これら 2 つの処理を組み合わせて可能であれば、識別子を実際のプロパティにマップし、構成の両方に。</span><span class="sxs-lookup"><span data-stu-id="7f799-126">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

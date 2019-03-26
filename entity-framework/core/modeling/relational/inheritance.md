---
title: 継承 (リレーショナル データベース) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 2aaceb05bbc1b0eb5c116b3dc1fb33c90c115a70
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419680"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="6c7ff-102">継承 (リレーショナル データベース)</span><span class="sxs-lookup"><span data-stu-id="6c7ff-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="6c7ff-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6c7ff-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6c7ff-105">EF モデルでの継承は、データベース内でエンティティ クラスの継承を表現する方法を制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="6c7ff-106">現時点では、EF Core での table-per-hierarchy (TPH) パターンのみが実装されます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="6c7ff-107">テーブルあたり型 (TPT) などの他の一般的なパターンとテーブル-、具象型の種類 (TPC) はまだ提供されません。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="6c7ff-108">規約</span><span class="sxs-lookup"><span data-stu-id="6c7ff-108">Conventions</span></span>

<span data-ttu-id="6c7ff-109">慣例により、継承を-table-per-hierarchy (TPH) パターンを使用してマップされます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="6c7ff-110">TPH は、階層内のすべての種類のデータを格納するのに 1 つのテーブルを使用します。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="6c7ff-111">識別子列を使用して、各行を表す種類を識別します。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="6c7ff-112">2 つまたは複数の継承された型がモデルで明示的に含まれている場合は、EF Core に継承はセットアップのみ (を参照してください[継承](../inheritance.md)の詳細)。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="6c7ff-113">単純な継承シナリオおよび TPH パターンを使用してリレーショナル データベース テーブルに格納されているデータの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="6c7ff-114">*識別子*列の型を指定する*ブログ*行ごとに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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

>[!NOTE]
> <span data-ttu-id="6c7ff-116">データベース colmmns は TPH マッピングを使用する場合、必要に応じて、null 許容に自動的にします。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-116">Database colmmns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6c7ff-117">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6c7ff-117">Data Annotations</span></span>

<span data-ttu-id="6c7ff-118">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6c7ff-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6c7ff-119">Fluent API</span></span>

<span data-ttu-id="6c7ff-120">Fluent API を使用して、名前と識別子列と、階層内の各型を識別するために使用される値の型を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="6c7ff-121">識別子のプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-121">Configuring the discriminator property</span></span>

<span data-ttu-id="6c7ff-122">上記の例では、識別子として作成されます、[プロパティをシャドウ](xref:core/modeling/shadow-properties)階層の基本エンティティです。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="6c7ff-123">モデルのプロパティであるために、その他のプロパティと同じように構成できます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="6c7ff-124">たとえば、識別子の規則によって、既定値が使用されているときに、最大長を設定します。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="6c7ff-125">識別子は、エンティティ内の実際の CLR プロパティにもマップできます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="6c7ff-126">例:</span><span class="sxs-lookup"><span data-stu-id="6c7ff-126">For example:</span></span>
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

<span data-ttu-id="6c7ff-127">これら 2 つを組み合わせることは、識別子を実際のプロパティにマップし、構成の両方にできます。</span><span class="sxs-lookup"><span data-stu-id="6c7ff-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

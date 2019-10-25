---
title: 継承 (リレーショナルデータベース)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: c660107619470a726fe13ad8eee2850749e6dcd9
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812083"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="faba4-102">継承 (リレーショナル データベース)</span><span class="sxs-lookup"><span data-stu-id="faba4-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="faba4-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="faba4-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="faba4-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="faba4-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="faba4-105">EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="faba4-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="faba4-106">現時点では、EF Core で実装されているのは、階層単位 (TPH) パターンだけです。</span><span class="sxs-lookup"><span data-stu-id="faba4-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="faba4-107">テーブルごとのテーブル (TPT) や具象型ごとのテーブル (TPC) などのその他の一般的なパターンは、まだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="faba4-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="faba4-108">規約</span><span class="sxs-lookup"><span data-stu-id="faba4-108">Conventions</span></span>

<span data-ttu-id="faba4-109">慣例により、継承は、階層単位 (TPH) のパターンを使用してマップされます。</span><span class="sxs-lookup"><span data-stu-id="faba4-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="faba4-110">TPH は、1つのテーブルを使用して、階層内のすべての型のデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="faba4-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="faba4-111">識別子列は、各行が表す型を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="faba4-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="faba4-112">EF Core は、継承された複数の型がモデルに明示的に含まれている場合にのみ継承を設定します (詳細については、「[継承](../inheritance.md)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="faba4-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="faba4-113">単純な継承シナリオと、TPH パターンを使用してリレーショナルデータベーステーブルに格納されているデータを示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="faba4-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="faba4-114">*識別子*列には、各行に格納されている*ブログ*の種類が示されます。</span><span class="sxs-lookup"><span data-stu-id="faba4-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/InheritanceDbSets.cs)] -->
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
> <span data-ttu-id="faba4-116">TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。</span><span class="sxs-lookup"><span data-stu-id="faba4-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="faba4-117">データの注釈</span><span class="sxs-lookup"><span data-stu-id="faba4-117">Data Annotations</span></span>

<span data-ttu-id="faba4-118">データ注釈を使用して継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="faba4-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="faba4-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="faba4-119">Fluent API</span></span>

<span data-ttu-id="faba4-120">Fluent API を使用して、識別子列の名前と型、および階層内の各型を識別するために使用される値を構成できます。</span><span class="sxs-lookup"><span data-stu-id="faba4-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="faba4-121">識別子プロパティの構成</span><span class="sxs-lookup"><span data-stu-id="faba4-121">Configuring the discriminator property</span></span>

<span data-ttu-id="faba4-122">上の例では、識別子は、階層の基本エンティティの[shadow プロパティ](xref:core/modeling/shadow-properties)として作成されます。</span><span class="sxs-lookup"><span data-stu-id="faba4-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="faba4-123">モデルのプロパティであるため、他のプロパティと同様に構成できます。</span><span class="sxs-lookup"><span data-stu-id="faba4-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="faba4-124">たとえば、既定の規則に従った識別子を使用する場合の最大長を設定するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="faba4-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="faba4-125">識別子は、エンティティ内の実際の CLR プロパティにマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="faba4-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="faba4-126">(例:</span><span class="sxs-lookup"><span data-stu-id="faba4-126">For example:</span></span>

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

<span data-ttu-id="faba4-127">これらの2つの要素を組み合わせて、識別子を実際のプロパティにマップして構成することができます。</span><span class="sxs-lookup"><span data-stu-id="faba4-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```

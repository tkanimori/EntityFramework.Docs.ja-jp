---
title: "継承 (リレーショナル データベース) の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
ms.technology: entity-framework-core
uid: core/modeling/relational/inheritance
ms.openlocfilehash: a7f697dfe2b93c7b93a2dd14945732db4f37628c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="d98ae-102">継承 (リレーショナル データベース)</span><span class="sxs-lookup"><span data-stu-id="d98ae-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="d98ae-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d98ae-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="d98ae-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="d98ae-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="d98ae-105">EF モデルでの継承を使用すると、データベース内のエンティティ クラスの継承の表現方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="d98ae-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="d98ae-106">規則</span><span class="sxs-lookup"><span data-stu-id="d98ae-106">Conventions</span></span>

<span data-ttu-id="d98ae-107">規則では、継承をテーブルあたり階層 (TPH) パターンを使用してマップされます。</span><span class="sxs-lookup"><span data-stu-id="d98ae-107">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="d98ae-108">TPH では、1 つのテーブルを使用して、階層内のすべての種類のデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="d98ae-108">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="d98ae-109">識別子の列は、各行が表すどの種類の識別に使用します。</span><span class="sxs-lookup"><span data-stu-id="d98ae-109">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="d98ae-110">2 つまたは複数の継承された型がモデルで明示的に含まれている場合は、EF に継承はセットアップだけ (を参照してください[継承](../inheritance.md)詳細)。</span><span class="sxs-lookup"><span data-stu-id="d98ae-110">EF will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="d98ae-111">以下には、単純な継承シナリオと TPH パターンを使用してリレーショナル データベースのテーブルに格納されたデータを示す例を示します。</span><span class="sxs-lookup"><span data-stu-id="d98ae-111">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="d98ae-112">*識別子*列の種類を識別する*ブログ*は行ごとに格納します。</span><span class="sxs-lookup"><span data-stu-id="d98ae-112">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="d98ae-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="d98ae-114">Data Annotations</span></span>

<span data-ttu-id="d98ae-115">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="d98ae-115">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="d98ae-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d98ae-116">Fluent API</span></span>

<span data-ttu-id="d98ae-117">Fluent API を使用して、名前と識別子の列と、階層内の各型の識別に使用される値の型を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="d98ae-117">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

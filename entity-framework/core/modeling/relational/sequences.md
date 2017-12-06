---
title: "シーケンスの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
ms.technology: entity-framework-core
uid: core/modeling/relational/sequences
ms.openlocfilehash: 98a40aeecbec0fd9fb9cc108d6b5f98178dea403
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="sequences"></a><span data-ttu-id="db216-102">シーケンス</span><span class="sxs-lookup"><span data-stu-id="db216-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="db216-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="db216-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="db216-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="db216-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="db216-105">シーケンスは、データベースの連続した数値の値を生成します。</span><span class="sxs-lookup"><span data-stu-id="db216-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="db216-106">シーケンスでは、特定のテーブルに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="db216-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="db216-107">規則</span><span class="sxs-lookup"><span data-stu-id="db216-107">Conventions</span></span>

<span data-ttu-id="db216-108">慣例により、シーケンスできませんで導入されたモデルにします。</span><span class="sxs-lookup"><span data-stu-id="db216-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="db216-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="db216-109">Data Annotations</span></span>

<span data-ttu-id="db216-110">データ注釈を使用してシーケンスを構成することがありません。</span><span class="sxs-lookup"><span data-stu-id="db216-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="db216-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="db216-111">Fluent API</span></span>

<span data-ttu-id="db216-112">Fluent API を使用して、モデルのシーケンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="db216-112">You can use the Fluent API to create a sequence in the model.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/Sequence.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="db216-113">そのスキーマ、開始値および増分値などのシーケンスの他の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="db216-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/SequenceConfigured.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);
    }
}
```

<span data-ttu-id="db216-114">シーケンスが導入された後は、モデル内のプロパティの値を生成するのに使用できます。</span><span class="sxs-lookup"><span data-stu-id="db216-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="db216-115">たとえば、使用することができます[既定値の](default-values.md)シーケンスから次の値を挿入します。</span><span class="sxs-lookup"><span data-stu-id="db216-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/SequenceUsed.cs?highlight=11,12,13)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);

        modelBuilder.Entity<Order>()
            .Property(o => o.OrderNo)
            .HasDefaultValueSql("NEXT VALUE FOR shared.OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```

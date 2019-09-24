---
title: シーケンス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: ce02b9840e58102a60c1d8eacf6810365104d7d7
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196909"
---
# <a name="sequences"></a><span data-ttu-id="2f8c8-102">シーケンス</span><span class="sxs-lookup"><span data-stu-id="2f8c8-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="2f8c8-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="2f8c8-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="2f8c8-105">シーケンスでは、データベース内に連続する数値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="2f8c8-106">シーケンスは、特定のテーブルに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="2f8c8-107">規約</span><span class="sxs-lookup"><span data-stu-id="2f8c8-107">Conventions</span></span>

<span data-ttu-id="2f8c8-108">慣例により、シーケンスはモデルには導入されません。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="2f8c8-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="2f8c8-109">Data Annotations</span></span>

<span data-ttu-id="2f8c8-110">データ注釈を使用してシーケンスを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="2f8c8-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="2f8c8-111">Fluent API</span></span>

<span data-ttu-id="2f8c8-112">Fluent API を使用して、モデル内にシーケンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-112">You can use the Fluent API to create a sequence in the model.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/Sequence.cs?highlight=7)] -->
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

<span data-ttu-id="2f8c8-113">また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceConfigured.cs?highlight=7,8,9)] -->
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

<span data-ttu-id="2f8c8-114">シーケンスが導入されたら、それを使用して、モデル内のプロパティの値を生成できます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="2f8c8-115">たとえば、[既定値](default-values.md)を使用して、シーケンスから次の値を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="2f8c8-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceUsed.cs?highlight=11,12,13)] -->
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

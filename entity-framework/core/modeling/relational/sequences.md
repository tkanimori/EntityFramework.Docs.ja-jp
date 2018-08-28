---
title: シーケンス - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: eb9d9896966af0ad6b778047a1ed6af7358e8eb2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994517"
---
# <a name="sequences"></a><span data-ttu-id="f0dd4-102">シーケンス</span><span class="sxs-lookup"><span data-stu-id="f0dd4-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="f0dd4-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="f0dd4-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="f0dd4-105">シーケンスは、データベースの連続した数値の値を生成します。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="f0dd4-106">シーケンスでは、特定のテーブルに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="f0dd4-107">規約</span><span class="sxs-lookup"><span data-stu-id="f0dd4-107">Conventions</span></span>

<span data-ttu-id="f0dd4-108">慣例により、シーケンスが導入されましたでモデルにします。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="f0dd4-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="f0dd4-109">Data Annotations</span></span>

<span data-ttu-id="f0dd4-110">データ注釈を使用して、シーケンスを構成することがありません。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f0dd4-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f0dd4-111">Fluent API</span></span>

<span data-ttu-id="f0dd4-112">Fluent API を使用して、モデルのシーケンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-112">You can use the Fluent API to create a sequence in the model.</span></span>

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

<span data-ttu-id="f0dd4-113">そのスキーマ、開始値、および増分値などのシーケンスの他の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

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

<span data-ttu-id="f0dd4-114">シーケンスが導入された後は、プロパティの値をモデルの生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="f0dd4-115">たとえば、使用することができます[既定値の](default-values.md)シーケンスから次の値を挿入します。</span><span class="sxs-lookup"><span data-stu-id="f0dd4-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

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

---
title: インデックスの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
ms.technology: entity-framework-core
uid: core/modeling/indexes
ms.openlocfilehash: f57b545d53613cec6887734bf434958ee8fff4d8
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26054886"
---
# <a name="indexes"></a><span data-ttu-id="01995-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="01995-102">Indexes</span></span>

<span data-ttu-id="01995-103">インデックスは、多くのデータ ストア間での一般的な概念です。</span><span class="sxs-lookup"><span data-stu-id="01995-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="01995-104">データ ストア内には、その実装は異なる場合があります、それらを使用して、列 (または列のセット) に基づく参照より効率的です。</span><span class="sxs-lookup"><span data-stu-id="01995-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="01995-105">規則</span><span class="sxs-lookup"><span data-stu-id="01995-105">Conventions</span></span>

<span data-ttu-id="01995-106">慣例により、各プロパティ (または一連のプロパティ) で外部キーとして使用されるインデックスを作成します。</span><span class="sxs-lookup"><span data-stu-id="01995-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="01995-107">データの注釈</span><span class="sxs-lookup"><span data-stu-id="01995-107">Data Annotations</span></span>

<span data-ttu-id="01995-108">データ注釈を使用してインデックスを作成できないことができます。</span><span class="sxs-lookup"><span data-stu-id="01995-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="01995-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="01995-109">Fluent API</span></span>

<span data-ttu-id="01995-110">Fluent API を使用すると、1 つのプロパティに、インデックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="01995-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="01995-111">既定では、インデックスが一意でないです。</span><span class="sxs-lookup"><span data-stu-id="01995-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Index.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="01995-112">指定できます、インデックスが一意にする必要がありますいない 2 つのエンティティが指定されたプロパティの同じ値を持てることを意味します。</span><span class="sxs-lookup"><span data-stu-id="01995-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="01995-113">インデックスは、1 つ以上の列にも指定できます。</span><span class="sxs-lookup"><span data-stu-id="01995-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .HasIndex(p => new { p.FirstName, p.LastName });
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="01995-114">プロパティの個別のセットごとの 1 つだけのインデックスがあります。</span><span class="sxs-lookup"><span data-stu-id="01995-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="01995-115">Fluent API を使用して一連の規則または以前の構成によってプロパティが既に定義されている、インデックスでインデックスを構成する場合そのインデックスの定義は、変更するされます。</span><span class="sxs-lookup"><span data-stu-id="01995-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="01995-116">これは、さらに規約によって作成されたインデックスを構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="01995-116">This is useful if you want to further configure an index that was created by convention.</span></span>

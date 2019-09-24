---
title: インデックス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: b6f11401b69bd8e8795f6b22e5392ba16fc9ba2e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197249"
---
# <a name="indexes"></a><span data-ttu-id="97446-102">Indexes</span><span class="sxs-lookup"><span data-stu-id="97446-102">Indexes</span></span>

<span data-ttu-id="97446-103">インデックスは、多くのデータストアで共通の概念です。</span><span class="sxs-lookup"><span data-stu-id="97446-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="97446-104">データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="97446-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="97446-105">規約</span><span class="sxs-lookup"><span data-stu-id="97446-105">Conventions</span></span>

<span data-ttu-id="97446-106">規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="97446-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="97446-107">データの注釈</span><span class="sxs-lookup"><span data-stu-id="97446-107">Data Annotations</span></span>

<span data-ttu-id="97446-108">データ注釈を使用してインデックスを作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="97446-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="97446-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="97446-109">Fluent API</span></span>

<span data-ttu-id="97446-110">Fluent API を使用して、1つのプロパティにインデックスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="97446-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="97446-111">既定では、インデックスは一意ではありません。</span><span class="sxs-lookup"><span data-stu-id="97446-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Index.cs?highlight=7,8)] -->
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

<span data-ttu-id="97446-112">また、インデックスが一意であることを指定することもできます。つまり、2つのエンティティが特定のプロパティに対して同じ値を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="97446-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="97446-113">複数の列に対してインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="97446-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexComposite.cs?highlight=7,8)] -->
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
> <span data-ttu-id="97446-114">個別のプロパティセットごとにインデックスが1つだけ存在します。</span><span class="sxs-lookup"><span data-stu-id="97446-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="97446-115">Fluent API を使用して、既にインデックスが定義されている一連のプロパティ (規則または以前の構成) のインデックスを構成する場合は、そのインデックスの定義を変更します。</span><span class="sxs-lookup"><span data-stu-id="97446-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="97446-116">これは、規則によって作成されたインデックスをさらに構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="97446-116">This is useful if you want to further configure an index that was created by convention.</span></span>

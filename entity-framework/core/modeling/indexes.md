---
title: インデックス - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 87fe893243377e3ab83d419ae9bedf813ca50c3f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995481"
---
# <a name="indexes"></a><span data-ttu-id="7e387-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="7e387-102">Indexes</span></span>

<span data-ttu-id="7e387-103">インデックスは、多くのデータ ストア間での一般的な概念です。</span><span class="sxs-lookup"><span data-stu-id="7e387-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="7e387-104">データ ストアでは、その実装が異なる場合があります、中を使用して、列 (または列のセット) に基づく参照の詳細は効率的です。</span><span class="sxs-lookup"><span data-stu-id="7e387-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="7e387-105">規約</span><span class="sxs-lookup"><span data-stu-id="7e387-105">Conventions</span></span>

<span data-ttu-id="7e387-106">慣例により、各プロパティ (またはプロパティのセット) で外部キーとして使用されるインデックスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e387-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7e387-107">データの注釈</span><span class="sxs-lookup"><span data-stu-id="7e387-107">Data Annotations</span></span>

<span data-ttu-id="7e387-108">データ注釈を使用してインデックスを作成しないことができます。</span><span class="sxs-lookup"><span data-stu-id="7e387-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="7e387-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7e387-109">Fluent API</span></span>

<span data-ttu-id="7e387-110">Fluent API を使用すると、1 つのプロパティのインデックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="7e387-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="7e387-111">既定では、インデックスが一意でないです。</span><span class="sxs-lookup"><span data-stu-id="7e387-111">By default, indexes are non-unique.</span></span>

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

<span data-ttu-id="7e387-112">指定することもインデックスが一意ではない 2 つのエンティティが指定されたプロパティの同じ値を使用できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7e387-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="7e387-113">1 つ以上の列にインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="7e387-113">You can also specify an index over more than one column.</span></span>

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
> <span data-ttu-id="7e387-114">プロパティの個別のセットごとの 1 つだけのインデックスがあります。</span><span class="sxs-lookup"><span data-stu-id="7e387-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="7e387-115">Fluent API を使用して、一連の規則または以前の構成によってプロパティが既に定義されている場合、インデックスでインデックスを構成する場合は、そのインデックスの定義変更するされます。</span><span class="sxs-lookup"><span data-stu-id="7e387-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="7e387-116">これは、規則によって作成されたインデックスをさらに構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="7e387-116">This is useful if you want to further configure an index that was created by convention.</span></span>

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
# <a name="indexes"></a>インデックス

インデックスは、多くのデータ ストア間での一般的な概念です。 データ ストアでは、その実装が異なる場合があります、中を使用して、列 (または列のセット) に基づく参照の詳細は効率的です。

## <a name="conventions"></a>規約

慣例により、各プロパティ (またはプロパティのセット) で外部キーとして使用されるインデックスを作成します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを作成しないことができます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用すると、1 つのプロパティのインデックスを指定します。 既定では、インデックスが一意でないです。

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

指定することもインデックスが一意ではない 2 つのエンティティが指定されたプロパティの同じ値を使用できることを意味します。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

1 つ以上の列にインデックスを指定することもできます。

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
> プロパティの個別のセットごとの 1 つだけのインデックスがあります。 Fluent API を使用して、一連の規則または以前の構成によってプロパティが既に定義されている場合、インデックスでインデックスを構成する場合は、そのインデックスの定義変更するされます。 これは、規則によって作成されたインデックスをさらに構成する場合に便利です。

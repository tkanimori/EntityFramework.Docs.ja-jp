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
# <a name="indexes"></a>Indexes

インデックスは、多くのデータストアで共通の概念です。 データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。

## <a name="conventions"></a>規約

規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを作成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、1つのプロパティにインデックスを指定できます。 既定では、インデックスは一意ではありません。

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

また、インデックスが一意であることを指定することもできます。つまり、2つのエンティティが特定のプロパティに対して同じ値を持つことはできません。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

複数の列に対してインデックスを指定することもできます。

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
> 個別のプロパティセットごとにインデックスが1つだけ存在します。 Fluent API を使用して、既にインデックスが定義されている一連のプロパティ (規則または以前の構成) のインデックスを構成する場合は、そのインデックスの定義を変更します。 これは、規則によって作成されたインデックスをさらに構成する場合に便利です。

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
# <a name="indexes"></a>インデックス

インデックスは、多くのデータ ストア間での一般的な概念です。 データ ストア内には、その実装は異なる場合があります、それらを使用して、列 (または列のセット) に基づく参照より効率的です。

## <a name="conventions"></a>規則

慣例により、各プロパティ (または一連のプロパティ) で外部キーとして使用されるインデックスを作成します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを作成できないことができます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用すると、1 つのプロパティに、インデックスを指定します。 既定では、インデックスが一意でないです。

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

指定できます、インデックスが一意にする必要がありますいない 2 つのエンティティが指定されたプロパティの同じ値を持てることを意味します。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

インデックスは、1 つ以上の列にも指定できます。

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
> プロパティの個別のセットごとの 1 つだけのインデックスがあります。 Fluent API を使用して一連の規則または以前の構成によってプロパティが既に定義されている、インデックスでインデックスを構成する場合そのインデックスの定義は、変更するされます。 これは、さらに規約によって作成されたインデックスを構成する場合に便利です。

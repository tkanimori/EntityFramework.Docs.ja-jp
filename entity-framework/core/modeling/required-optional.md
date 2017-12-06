---
title: "必須/オプションのプロパティ - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
ms.technology: entity-framework-core
uid: core/modeling/required-optional
ms.openlocfilehash: 2af1d49e12ef980f81cb9c00556dee471673ccae
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="required-and-optional-properties"></a>必須およびオプションのプロパティ

プロパティを含めるには有効である場合は省略可能と見なされます`null`です。 場合`null`必須プロパティであると見なされますし、プロパティに割り当てられるに有効な値ではありません。

## <a name="conventions"></a>規則

規則では、プロパティの CLR 型が null を含めることができます、構成するオプションとして (`string`、 `int?`、 `byte[]`, などです。)。 CLR 型を持つは null を含めることはできませんのプロパティは構成されている必要に応じて (`int`、 `decimal`、 `bool`, などです。)。

> [!NOTE]  
> CLR 型を null に含めることはできません、プロパティは、省略可能として構成できません。 常に Entity Framework によって必要なプロパティが考慮されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティが必要であることを示すことができます。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [Required]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>Fluent API

プロパティが必要であることを示す Fluent API を使用することができます。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

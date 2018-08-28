---
title: 必須/任意のプロパティ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: b6716a5b03e1afc2933e317d606ef50f986c22c7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995498"
---
# <a name="required-and-optional-properties"></a>必須およびオプションのプロパティ

プロパティを含めるには有効な場合は省略可能なと見なされます`null`します。 場合`null`し、必要なプロパティであると見なされるプロパティに割り当てられる有効な値ではありません。

## <a name="conventions"></a>規約

慣例により、プロパティの CLR 型が null を含めることができますが構成するオプションとして (`string`、 `int?`、`byte[]`など。)。 CLR 型が null を含めることはできませんのプロパティを構成は必要に応じて (`int`、 `decimal`、`bool`など。)。

> [!NOTE]  
> CLR 型を null に含めることはできませんプロパティは、省略可能として構成できません。 常に Entity Framework によって必要なプロパティが考慮されます。

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

プロパティが必要であることを示す、Fluent API を使用することができます。

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

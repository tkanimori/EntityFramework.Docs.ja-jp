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
# <a name="required-and-optional-properties"></a><span data-ttu-id="ae735-102">必須およびオプションのプロパティ</span><span class="sxs-lookup"><span data-stu-id="ae735-102">Required and Optional Properties</span></span>

<span data-ttu-id="ae735-103">プロパティを含めるには有効である場合は省略可能と見なされます`null`です。</span><span class="sxs-lookup"><span data-stu-id="ae735-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="ae735-104">場合`null`必須プロパティであると見なされますし、プロパティに割り当てられるに有効な値ではありません。</span><span class="sxs-lookup"><span data-stu-id="ae735-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="ae735-105">規則</span><span class="sxs-lookup"><span data-stu-id="ae735-105">Conventions</span></span>

<span data-ttu-id="ae735-106">規則では、プロパティの CLR 型が null を含めることができます、構成するオプションとして (`string`、 `int?`、 `byte[]`, などです。)。</span><span class="sxs-lookup"><span data-stu-id="ae735-106">By convention, a property whose CLR type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="ae735-107">CLR 型を持つは null を含めることはできませんのプロパティは構成されている必要に応じて (`int`、 `decimal`、 `bool`, などです。)。</span><span class="sxs-lookup"><span data-stu-id="ae735-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="ae735-108">CLR 型を null に含めることはできません、プロパティは、省略可能として構成できません。</span><span class="sxs-lookup"><span data-stu-id="ae735-108">A property whose CLR type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="ae735-109">常に Entity Framework によって必要なプロパティが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="ae735-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ae735-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="ae735-110">Data Annotations</span></span>

<span data-ttu-id="ae735-111">データ注釈を使用して、プロパティが必要であることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="ae735-111">You can use Data Annotations to indicate that a property is required.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [Required]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="ae735-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ae735-112">Fluent API</span></span>

<span data-ttu-id="ae735-113">プロパティが必要であることを示す Fluent API を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ae735-113">You can use the Fluent API to indicate that a property is required.</span></span>

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

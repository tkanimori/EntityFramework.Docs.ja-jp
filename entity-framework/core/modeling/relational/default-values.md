---
title: "既定値 - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
ms.technology: entity-framework-core
uid: core/modeling/relational/default-values
ms.openlocfilehash: 73b916b6d9f9c984c8ea010f2319eafa7d031a58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="default-values"></a><span data-ttu-id="27e87-102">既定値</span><span class="sxs-lookup"><span data-stu-id="27e87-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="27e87-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="27e87-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="27e87-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="27e87-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="27e87-105">列の既定値は、新しい行が挿入されていますが、列の値が指定されていない場合に挿入される値です。</span><span class="sxs-lookup"><span data-stu-id="27e87-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="27e87-106">規則</span><span class="sxs-lookup"><span data-stu-id="27e87-106">Conventions</span></span>

<span data-ttu-id="27e87-107">規則では、既定値が構成されていません。</span><span class="sxs-lookup"><span data-stu-id="27e87-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="27e87-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="27e87-108">Data Annotations</span></span>

<span data-ttu-id="27e87-109">データ注釈を使用して、既定値は設定できません。</span><span class="sxs-lookup"><span data-stu-id="27e87-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="27e87-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="27e87-110">Fluent API</span></span>

<span data-ttu-id="27e87-111">Fluent API を使用すると、プロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="27e87-111">You can use the Fluent API to specify the default value for a property.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultValue.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Rating)
            .HasDefaultValue(3);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public int Rating { get; set; }
}
```

<span data-ttu-id="27e87-112">既定値の計算に使用される SQL フラグメントを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="27e87-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultValueSql.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Created)
            .HasDefaultValueSql("getdate()");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public DateTime Created { get; set; }
}
```

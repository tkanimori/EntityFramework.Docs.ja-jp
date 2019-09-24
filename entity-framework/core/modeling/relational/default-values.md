---
title: 既定値-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: 0d3613606f21a78e22cfe0ee752ea982a6a17f93
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196985"
---
# <a name="default-values"></a><span data-ttu-id="77ac0-102">既定値</span><span class="sxs-lookup"><span data-stu-id="77ac0-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="77ac0-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="77ac0-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="77ac0-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="77ac0-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="77ac0-105">列の既定値は、新しい行が挿入されても列に値が指定されていない場合に挿入される値です。</span><span class="sxs-lookup"><span data-stu-id="77ac0-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="77ac0-106">規約</span><span class="sxs-lookup"><span data-stu-id="77ac0-106">Conventions</span></span>

<span data-ttu-id="77ac0-107">慣例により、既定値は構成されていません。</span><span class="sxs-lookup"><span data-stu-id="77ac0-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="77ac0-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="77ac0-108">Data Annotations</span></span>

<span data-ttu-id="77ac0-109">データ注釈を使用して既定値を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="77ac0-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="77ac0-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="77ac0-110">Fluent API</span></span>

<span data-ttu-id="77ac0-111">Fluent API を使用して、プロパティの既定値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="77ac0-111">You can use the Fluent API to specify the default value for a property.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultValue.cs?highlight=9)] -->
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

<span data-ttu-id="77ac0-112">既定値の計算に使用される SQL フラグメントを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="77ac0-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultValueSql.cs?highlight=9)] -->
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

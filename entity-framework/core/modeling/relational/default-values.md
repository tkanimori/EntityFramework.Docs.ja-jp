---
title: 既定値 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: 341f243ddddc345bb4236e5c34f814694b71e32a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996253"
---
# <a name="default-values"></a><span data-ttu-id="515d8-102">既定値</span><span class="sxs-lookup"><span data-stu-id="515d8-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="515d8-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="515d8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="515d8-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="515d8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="515d8-105">列の既定値は、新しい行が挿入されますが、列の値が指定されていない場合に挿入される値です。</span><span class="sxs-lookup"><span data-stu-id="515d8-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="515d8-106">規約</span><span class="sxs-lookup"><span data-stu-id="515d8-106">Conventions</span></span>

<span data-ttu-id="515d8-107">慣例により、既定値が構成されていません。</span><span class="sxs-lookup"><span data-stu-id="515d8-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="515d8-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="515d8-108">Data Annotations</span></span>

<span data-ttu-id="515d8-109">データ注釈を使用して既定値を設定することができません。</span><span class="sxs-lookup"><span data-stu-id="515d8-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="515d8-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="515d8-110">Fluent API</span></span>

<span data-ttu-id="515d8-111">Fluent API を使用すると、プロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="515d8-111">You can use the Fluent API to specify the default value for a property.</span></span>

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

<span data-ttu-id="515d8-112">既定値を計算に使用される SQL フラグメントを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="515d8-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

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

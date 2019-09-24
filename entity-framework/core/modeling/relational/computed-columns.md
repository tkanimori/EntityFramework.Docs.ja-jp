---
title: 計算列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: da106c94698a202744d7cd465aa84d0d72802833
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197232"
---
# <a name="computed-columns"></a><span data-ttu-id="6b53b-102">計算列</span><span class="sxs-lookup"><span data-stu-id="6b53b-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="6b53b-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="6b53b-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6b53b-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6b53b-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6b53b-105">計算列とは、データベース内で値が計算される列のことです。</span><span class="sxs-lookup"><span data-stu-id="6b53b-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="6b53b-106">計算列では、テーブル内の他の列を使用してその値を計算できます。</span><span class="sxs-lookup"><span data-stu-id="6b53b-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="6b53b-107">規約</span><span class="sxs-lookup"><span data-stu-id="6b53b-107">Conventions</span></span>

<span data-ttu-id="6b53b-108">慣例により、計算列はモデルに作成されません。</span><span class="sxs-lookup"><span data-stu-id="6b53b-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6b53b-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6b53b-109">Data Annotations</span></span>

<span data-ttu-id="6b53b-110">計算列は、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b53b-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6b53b-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6b53b-111">Fluent API</span></span>

<span data-ttu-id="6b53b-112">Fluent API を使用して、プロパティを計算列にマップする必要があることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="6b53b-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/ComputedColumn.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(p => p.DisplayName)
            .HasComputedColumnSql("[LastName] + ', ' + [FirstName]");
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string DisplayName { get; set; }
}
```

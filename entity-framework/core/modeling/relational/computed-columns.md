---
title: 計算列の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
ms.technology: entity-framework-core
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 95312504286bd34cc666b5a21273835c4b35d379
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052482"
---
# <a name="computed-columns"></a><span data-ttu-id="4e713-102">計算列</span><span class="sxs-lookup"><span data-stu-id="4e713-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="4e713-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e713-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="4e713-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="4e713-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="4e713-105">計算列は、その値は、データベース内計算列です。</span><span class="sxs-lookup"><span data-stu-id="4e713-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="4e713-106">計算列は、表内の他の列を使用して、その値を計算します。</span><span class="sxs-lookup"><span data-stu-id="4e713-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="4e713-107">規則</span><span class="sxs-lookup"><span data-stu-id="4e713-107">Conventions</span></span>

<span data-ttu-id="4e713-108">慣例により、計算列は、モデルでは作成されません。</span><span class="sxs-lookup"><span data-stu-id="4e713-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4e713-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="4e713-109">Data Annotations</span></span>

<span data-ttu-id="4e713-110">データ注釈を使用した計算列を構成できません。</span><span class="sxs-lookup"><span data-stu-id="4e713-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="4e713-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="4e713-111">Fluent API</span></span>

<span data-ttu-id="4e713-112">Fluent API を使用して、プロパティが計算列にマップすることを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="4e713-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/ComputedColumn.cs?highlight=9)] -->
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

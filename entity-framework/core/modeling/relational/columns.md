---
title: "列のマッピング - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
ms.technology: entity-framework-core
uid: core/modeling/relational/columns
ms.openlocfilehash: 697b966dbac892e332fe65feaa4dd11f00dd8298
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="column-mapping"></a><span data-ttu-id="8f35a-102">列のマッピング</span><span class="sxs-lookup"><span data-stu-id="8f35a-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="8f35a-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="8f35a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="8f35a-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="8f35a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="8f35a-105">列マッピングは、列データをからクエリし、データベース内に保存する必要がありますを識別します。</span><span class="sxs-lookup"><span data-stu-id="8f35a-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="8f35a-106">規則</span><span class="sxs-lookup"><span data-stu-id="8f35a-106">Conventions</span></span>

<span data-ttu-id="8f35a-107">慣例により、各プロパティをプロパティと同じ名前の列にマップするセットアップとなります。</span><span class="sxs-lookup"><span data-stu-id="8f35a-107">By convention, each property will be setup to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8f35a-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8f35a-108">Data Annotations</span></span>

<span data-ttu-id="8f35a-109">データ注釈を使用して、プロパティがマップされている列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8f35a-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=3)] -->
``` csharp
public class Blog
{
    [Column("blog_id")]
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="8f35a-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8f35a-110">Fluent API</span></span>

<span data-ttu-id="8f35a-111">Fluent API を使用して、プロパティがマップされている列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8f35a-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.BlogId)
            .HasColumnName("blog_id");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

---
title: '& 型を除外する (EF Core)'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: ca83b1c432bdf4853dba81e12ec4a739bc8218dc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197377"
---
# <a name="including--excluding-types"></a><span data-ttu-id="0bde4-102">種類を含める/除外する</span><span class="sxs-lookup"><span data-stu-id="0bde4-102">Including & Excluding Types</span></span>

<span data-ttu-id="0bde4-103">モデルに型を含めると、EF にはその型に関するメタデータが含まれ、データベースとの間でインスタンスの読み取りおよび書き込みが試行されます。</span><span class="sxs-lookup"><span data-stu-id="0bde4-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="0bde4-104">規約</span><span class="sxs-lookup"><span data-stu-id="0bde4-104">Conventions</span></span>

<span data-ttu-id="0bde4-105">慣例により、コンテキストのプロパティで`DbSet`公開される型は、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="0bde4-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="0bde4-106">さらに、 `OnModelCreating`メソッドに記述されている型も含まれています。</span><span class="sxs-lookup"><span data-stu-id="0bde4-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="0bde4-107">最後に、検出された型のナビゲーションプロパティを再帰的に調べることによって検出された型も、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="0bde4-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="0bde4-108">**たとえば、次のコードでは、3種類すべてが検出されています。**</span><span class="sxs-lookup"><span data-stu-id="0bde4-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="0bde4-109">`Blog`コンテキストの`DbSet`プロパティで公開されているため</span><span class="sxs-lookup"><span data-stu-id="0bde4-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="0bde4-110">`Post`ナビゲーションプロパティを使用し`Blog.Posts`て検出されるため、</span><span class="sxs-lookup"><span data-stu-id="0bde4-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="0bde4-111">`AuditEntry`これは、「」で説明されています。`OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="0bde4-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="0bde4-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="0bde4-112">Data Annotations</span></span>

<span data-ttu-id="0bde4-113">データ注釈を使用して、モデルから型を除外できます。</span><span class="sxs-lookup"><span data-stu-id="0bde4-113">You can use Data Annotations to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a><span data-ttu-id="0bde4-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="0bde4-114">Fluent API</span></span>

<span data-ttu-id="0bde4-115">Fluent API を使用して、モデルから型を除外できます。</span><span class="sxs-lookup"><span data-stu-id="0bde4-115">You can use the Fluent API to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]

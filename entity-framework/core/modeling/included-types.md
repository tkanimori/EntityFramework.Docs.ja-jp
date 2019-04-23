---
title: 含める/除外する型 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: f533b24312af37634ce4957e43c39ce776bf0bf0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929798"
---
# <a name="including--excluding-types"></a><span data-ttu-id="ff449-102">含める/除外する型</span><span class="sxs-lookup"><span data-stu-id="ff449-102">Including & Excluding Types</span></span>

<span data-ttu-id="ff449-103">種類を含むにモデルを EF がに関するメタデータを持つ入力し、読み取りし、書き込みデータベースのインスタンスを試みます。</span><span class="sxs-lookup"><span data-stu-id="ff449-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="ff449-104">規約</span><span class="sxs-lookup"><span data-stu-id="ff449-104">Conventions</span></span>

<span data-ttu-id="ff449-105">慣例により、型で公開されている`DbSet`プロパティのコンテキストで、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff449-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="ff449-106">さらに記載されている型、`OnModelCreating`メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff449-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="ff449-107">最後に、検出された型のナビゲーション プロパティの調査を再帰的にある任意の型は、モデルにも含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff449-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="ff449-108">**たとえば、次のコード リストですべての 3 種類が検出されます。**</span><span class="sxs-lookup"><span data-stu-id="ff449-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="ff449-109">`Blog` 公開されているため、`DbSet`コンテキストのプロパティ</span><span class="sxs-lookup"><span data-stu-id="ff449-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="ff449-110">`Post` 使用して検出されたため、`Blog.Posts`ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="ff449-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="ff449-111">`AuditEntry` 指定されているため、 `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="ff449-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
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

## <a name="data-annotations"></a><span data-ttu-id="ff449-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="ff449-112">Data Annotations</span></span>

<span data-ttu-id="ff449-113">データ注釈を使用して、モデルから型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="ff449-113">You can use Data Annotations to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a><span data-ttu-id="ff449-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ff449-114">Fluent API</span></span>

<span data-ttu-id="ff449-115">Fluent API を使用して、モデルから型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="ff449-115">You can use the Fluent API to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=12)]

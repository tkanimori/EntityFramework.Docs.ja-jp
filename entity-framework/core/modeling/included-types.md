---
title: 含む & 種類 - EF コアを除外します。
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
ms.technology: entity-framework-core
uid: core/modeling/included-types
ms.openlocfilehash: a8d7293a144968d2506bdcc76e55a1a0b1e3fd4b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052602"
---
# <a name="including--excluding-types"></a><span data-ttu-id="36c32-102">含むと種類を除外します。</span><span class="sxs-lookup"><span data-stu-id="36c32-102">Including & Excluding Types</span></span>

<span data-ttu-id="36c32-103">入力し、インスタンスから/データベースの読み書きを試みます EF にはに関するメタデータが含まれているモデル手段で型を含みます。</span><span class="sxs-lookup"><span data-stu-id="36c32-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="36c32-104">規則</span><span class="sxs-lookup"><span data-stu-id="36c32-104">Conventions</span></span>

<span data-ttu-id="36c32-105">規則で公開されている型`DbSet`コンテキストにプロパティが、モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="36c32-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="36c32-106">さらに記載されている型、`OnModelCreating`メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="36c32-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="36c32-107">最後に、再帰的に検出された型のナビゲーション プロパティを表示しているすべての型は、モデルにも含まれます。</span><span class="sxs-lookup"><span data-stu-id="36c32-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="36c32-108">**たとえば、次のコード リストで 3 種類すべてが検出されます。**</span><span class="sxs-lookup"><span data-stu-id="36c32-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="36c32-109">`Blog`公開されているため、`DbSet`コンテキストのプロパティ</span><span class="sxs-lookup"><span data-stu-id="36c32-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="36c32-110">`Post`使用して検出されるため、`Blog.Posts`ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="36c32-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="36c32-111">`AuditEntry`指定されているため`OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="36c32-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="36c32-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="36c32-112">Data Annotations</span></span>

<span data-ttu-id="36c32-113">データ注釈を使用して、モデルから型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="36c32-113">You can use Data Annotations to exclude a type from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=9)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogMetadata Metadata { get; set; }
}

[NotMapped]
public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="36c32-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="36c32-114">Fluent API</span></span>

<span data-ttu-id="36c32-115">Fluent API を使用して、モデルから型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="36c32-115">You can use the Fluent API to exclude a type from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Ignore<BlogMetadata>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogMetadata Metadata { get; set; }
}

public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

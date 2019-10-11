---
title: Entity Framework Core の概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e736251753134b716e64f24f6c517ed9f66a7db4
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181325"
---
# <a name="entity-framework-core"></a><span data-ttu-id="6c5b3-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6c5b3-102">Entity Framework Core</span></span>

<span data-ttu-id="6c5b3-103">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="6c5b3-104">EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="6c5b3-105">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="6c5b3-106">モデル</span><span class="sxs-lookup"><span data-stu-id="6c5b3-106">The Model</span></span>

<span data-ttu-id="6c5b3-107">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="6c5b3-108">モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="6c5b3-109">詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="6c5b3-110">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、[EF 移行](managing-schemas/migrations/index.md)を使ってモデルからデータベースを作成してから、モデルが時間の経過と共に変化するのに合わせてそれを進化させたりできます。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace Intro
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(
                @"Server=(localdb)\mssqllocaldb;Database=Blogging;Integrated Security=True");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }
        public int Rating { get; set; }
        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

## <a name="querying"></a><span data-ttu-id="6c5b3-111">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="6c5b3-111">Querying</span></span>

<span data-ttu-id="6c5b3-112">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="6c5b3-113">詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-113">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="6c5b3-114">データの保存</span><span class="sxs-lookup"><span data-stu-id="6c5b3-114">Saving Data</span></span>

<span data-ttu-id="6c5b3-115">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="6c5b3-116">詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-116">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

## <a name="next-steps"></a><span data-ttu-id="6c5b3-117">次の手順</span><span class="sxs-lookup"><span data-stu-id="6c5b3-117">Next steps</span></span>

<span data-ttu-id="6c5b3-118">入門チュートリアルについては、「[Entity Framework Core の概要](get-started/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5b3-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>


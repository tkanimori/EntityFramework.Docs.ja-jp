---
title: "概要 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
ms.technology: entity-framework-core
uid: core/index
ms.openlocfilehash: c76b4cd318151b502c549fa0a82800f9987ed94c
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="entity-framework-core-quick-overview"></a><span data-ttu-id="456c2-102">Entity Framework Core 概要</span><span class="sxs-lookup"><span data-stu-id="456c2-102">Entity Framework Core Quick Overview</span></span>

<span data-ttu-id="456c2-103">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="456c2-103">Entity Framework (EF) Core is a lightweight, extensible, and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="456c2-104">EF Core はオブジェクト リレーショナル マッパー (O/RM) であり、.NET 開発者は .NET オブジェクトを利用してデータベースを操作できます。</span><span class="sxs-lookup"><span data-stu-id="456c2-104">EF Core is an object-relational mapper (O/RM) that enables .NET developers to work with a database using .NET objects.</span></span> <span data-ttu-id="456c2-105">開発者が通常記述しなければならないデータアクセス コードの多くが不要になります。</span><span class="sxs-lookup"><span data-stu-id="456c2-105">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="456c2-106">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-106">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

<span data-ttu-id="456c2-107">コードを記述しながら学習したいのであれば、EF Core [入門](get-started/index.md)ガイドのご利用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="456c2-107">If you like to learn by writing code, we'd recommend one of our [Getting Started](get-started/index.md) guides to get you started with EF Core.</span></span>

## <a name="what-is-new-in-ef-core"></a><span data-ttu-id="456c2-108">EF Core の新機能</span><span class="sxs-lookup"><span data-stu-id="456c2-108">What is new in EF Core</span></span>

<span data-ttu-id="456c2-109">EF Core に精通しており、最新リリースの詳細をすぐに知りたい場合は、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-109">If you are familiar with EF Core and want to jump straight into the details of the latest releases:</span></span>

- <span data-ttu-id="456c2-110">**[EF Core 2.1 (現在プレビュー) の新機能](xref:core/what-is-new/ef-core-2.1)**</span><span class="sxs-lookup"><span data-stu-id="456c2-110">**[What is new in EF Core 2.1 (currently in preview)](xref:core/what-is-new/ef-core-2.1)**</span></span>
- <span data-ttu-id="456c2-111">**[EF Core 2.0 (最新リリース バージョン) の新機能](xref:core/what-is-new/ef-core-2.0)**</span><span class="sxs-lookup"><span data-stu-id="456c2-111">**[What is new in EF Core 2.0 (the latest released version)](xref:core/what-is-new/ef-core-2.0)**</span></span>
- <span data-ttu-id="456c2-112">**[既存アプリケーションを EF Core 2.0 にアップグレードする](xref:core/miscellaneous/1x-2x-upgrade)**</span><span class="sxs-lookup"><span data-stu-id="456c2-112">**[Upgrading existing applications to EF Core 2.0](xref:core/miscellaneous/1x-2x-upgrade)**</span></span>


## <a name="get-entity-framework-core"></a><span data-ttu-id="456c2-113">Entity Framework Core を入手する</span><span class="sxs-lookup"><span data-stu-id="456c2-113">Get Entity Framework Core</span></span>

<span data-ttu-id="456c2-114">使用するデータベース プロバイダーに対して [NuGet パッケージ](https://docs.nuget.org/ndocs/quickstart/use-a-package)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="456c2-114">[Install the NuGet package](https://docs.nuget.org/ndocs/quickstart/use-a-package) for the database provider you want to use.</span></span> <span data-ttu-id="456c2-115">たとえば、</span><span class="sxs-lookup"><span data-stu-id="456c2-115">E.g.</span></span> <span data-ttu-id="456c2-116">クロスプラットフォーム開発で SQL Server プロバイダーをインストールするためにコマンド ラインで `dotnet` ツールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="456c2-116">to install the SQL Server provider in cross-platform development using `dotnet` tool in the command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="456c2-117">あるいは、Visual Studio でパッケージ マネージャー コンソールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="456c2-117">Or in Visual Studio, using the Package Manager Console:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
<span data-ttu-id="456c2-118">利用できるプロバイダーについては「[Database Providers](providers/index.md)」 (データベース プロバイダー) を、インストール手順の詳細については「[Installing EF Core](get-started/install/index.md)」 (EF Core のインストール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-118">See [Database Providers](providers/index.md) for information on available providers and [Installing EF Core](get-started/install/index.md) for more detailed installation steps.</span></span>

## <a name="the-model"></a><span data-ttu-id="456c2-119">モデル</span><span class="sxs-lookup"><span data-stu-id="456c2-119">The Model</span></span>

<span data-ttu-id="456c2-120">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="456c2-120">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="456c2-121">モデルはエンティティ クラスと、データベースとのセッションを表す、派生コンテキストから構成されます。このセッションにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="456c2-121">A model is made up of entity classes and a derived context that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="456c2-122">詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-122">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="456c2-123">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、EF 移行を利用してモデルからデータベースを作成したり (モデルが時間の経過と共に変化するのに合わせ、進化させたり) できます。</span><span class="sxs-lookup"><span data-stu-id="456c2-123">You can generate a model from an existing database, hand code a model to match your database, or use EF Migrations to create a database from your model (and evolve it as your model changes over time).</span></span>

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
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
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

## <a name="querying"></a><span data-ttu-id="456c2-124">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="456c2-124">Querying</span></span>

<span data-ttu-id="456c2-125">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="456c2-125">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="456c2-126">詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-126">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="456c2-127">データの保存</span><span class="sxs-lookup"><span data-stu-id="456c2-127">Saving Data</span></span>

<span data-ttu-id="456c2-128">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="456c2-128">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="456c2-129">詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="456c2-129">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

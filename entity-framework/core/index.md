---
title: "概要 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
ms.technology: entity-framework-core
uid: core/index
ms.openlocfilehash: 13de9cf98111b8e253e073c591fcec04206b4c4f
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="entity-framework-core-quick-overview"></a><span data-ttu-id="588ea-102">Entity Framework Core 概要</span><span class="sxs-lookup"><span data-stu-id="588ea-102">Entity Framework Core Quick Overview</span></span>

<span data-ttu-id="588ea-103">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="588ea-103">Entity Framework (EF) Core is a lightweight, extensible, and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="588ea-104">EF Core はオブジェクト リレーショナル マッパー (O/RM) であり、.NET 開発者は .NET オブジェクトを利用してデータベースを操作できます。</span><span class="sxs-lookup"><span data-stu-id="588ea-104">EF Core is an object-relational mapper (O/RM) that enables .NET developers to work with a database using .NET objects.</span></span> <span data-ttu-id="588ea-105">開発者が通常記述しなければならないデータアクセス コードの多くが不要になります。</span><span class="sxs-lookup"><span data-stu-id="588ea-105">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="588ea-106">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-106">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

<span data-ttu-id="588ea-107">コードを記述しながら学習したいのであれば、EF Core [入門](get-started/index.md)ガイドのご利用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="588ea-107">If you like to learn by writing code, we'd recommend one of our [Getting Started](get-started/index.md) guides to get you started with EF Core.</span></span>

## <a name="latest-version-ef-core-20"></a><span data-ttu-id="588ea-108">最新版: EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="588ea-108">Latest version: EF Core 2.0</span></span>

<span data-ttu-id="588ea-109">EF Core に精通しており、新しいバージョンの詳細をすぐに知りたい場合は、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-109">If you are familiar with EF Core and want to jump straight into the details of the new version:</span></span>

- <span data-ttu-id="588ea-110">**[EF Core 2.0 の新機能](what-is-new/index.md)**</span><span class="sxs-lookup"><span data-stu-id="588ea-110">**[New features in EF Core 2.0](what-is-new/index.md)**</span></span>
- <span data-ttu-id="588ea-111">**[既存アプリケーションを EF Core 2.0 にアップグレードする](miscellaneous/1x-2x-upgrade.md)**</span><span class="sxs-lookup"><span data-stu-id="588ea-111">**[Upgrading existing applications to EF Core 2.0](miscellaneous/1x-2x-upgrade.md)**</span></span>

## <a name="get-entity-framework-core"></a><span data-ttu-id="588ea-112">Entity Framework Core を入手する</span><span class="sxs-lookup"><span data-stu-id="588ea-112">Get Entity Framework Core</span></span>

<span data-ttu-id="588ea-113">使用するデータベース プロバイダーに対して [NuGet パッケージ](https://docs.nuget.org/ndocs/quickstart/use-a-package)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="588ea-113">[Install the NuGet package](https://docs.nuget.org/ndocs/quickstart/use-a-package) for the database provider you want to use.</span></span> <span data-ttu-id="588ea-114">例:</span><span class="sxs-lookup"><span data-stu-id="588ea-114">E.g.</span></span> <span data-ttu-id="588ea-115">クロスプラットフォーム開発で SQL Server プロバイダーをインストールするためにコマンド ラインで `dotnet` ツールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="588ea-115">to install the SQL Server provider in cross-platform development using `dotnet` tool in the command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="588ea-116">あるいは、Visual Studio でパッケージ マネージャー コンソールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="588ea-116">Or in Visual Studio, using the Package Manager Console:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
<span data-ttu-id="588ea-117">利用できるプロバイダーについては「[Database Providers](providers/index.md)」 (データベース プロバイダー) を、インストール手順の詳細については「[Installing EF Core](get-started/install/index.md)」 (EF Core のインストール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-117">See [Database Providers](providers/index.md) for information on available providers and [Installing EF Core](get-started/install/index.md) for more detailed installation steps.</span></span>

## <a name="the-model"></a><span data-ttu-id="588ea-118">モデル</span><span class="sxs-lookup"><span data-stu-id="588ea-118">The Model</span></span>

<span data-ttu-id="588ea-119">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="588ea-119">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="588ea-120">モデルはエンティティ クラスと、データベースとのセッションを表す、派生コンテキストから構成されます。このセッションにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="588ea-120">A model is made up of entity classes and a derived context that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="588ea-121">詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-121">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="588ea-122">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、EF 移行を利用してモデルからデータベースを作成したり (モデルが時間の経過と共に変化するのに合わせ、進化させたり) できます。</span><span class="sxs-lookup"><span data-stu-id="588ea-122">You can generate a model from an existing database, hand code a model to match your database, or use EF Migrations to create a database from your model (and evolve it as your model changes over time).</span></span>

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

## <a name="querying"></a><span data-ttu-id="588ea-123">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="588ea-123">Querying</span></span>

<span data-ttu-id="588ea-124">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="588ea-124">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="588ea-125">詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-125">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="588ea-126">データの保存</span><span class="sxs-lookup"><span data-stu-id="588ea-126">Saving Data</span></span>

<span data-ttu-id="588ea-127">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="588ea-127">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="588ea-128">詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="588ea-128">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

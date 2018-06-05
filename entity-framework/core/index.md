---
title: 概要 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
ms.technology: entity-framework-core
uid: core/index
ms.openlocfilehash: 3befcbd3ff3da5dd159e6e6cb5fe7140c81317c2
ms.sourcegitcommit: a2b38dedc88ca3ccbfe7b1db9602ca02da8294cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2018
ms.locfileid: "34686663"
---
# <a name="entity-framework-core-quick-overview"></a><span data-ttu-id="0b47b-102">Entity Framework Core 概要</span><span class="sxs-lookup"><span data-stu-id="0b47b-102">Entity Framework Core Quick Overview</span></span>

<span data-ttu-id="0b47b-103">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="0b47b-103">Entity Framework (EF) Core is a lightweight, extensible, and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="0b47b-104">EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。</span><span class="sxs-lookup"><span data-stu-id="0b47b-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span> 

<span data-ttu-id="0b47b-105">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

<span data-ttu-id="0b47b-106">コードを記述しながら学習したいのであれば、EF Core [入門](get-started/index.md)ガイドのご利用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0b47b-106">If you like to learn by writing code, we'd recommend one of our [Getting Started](get-started/index.md) guides to get you started with EF Core.</span></span>

## <a name="what-is-new-in-ef-core"></a><span data-ttu-id="0b47b-107">EF Core の新機能</span><span class="sxs-lookup"><span data-stu-id="0b47b-107">What is new in EF Core</span></span>

<span data-ttu-id="0b47b-108">EF Core に精通しており、最新リリースの詳細をすぐに知りたい場合は、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-108">If you are familiar with EF Core and want to jump straight into the details of the latest releases:</span></span>

- <span data-ttu-id="0b47b-109">**[EF Core 2.1 の新機能](xref:core/what-is-new/ef-core-2.1)**</span><span class="sxs-lookup"><span data-stu-id="0b47b-109">**[What is new in EF Core 2.1](xref:core/what-is-new/ef-core-2.1)**</span></span>
- <span data-ttu-id="0b47b-110">**[既存アプリケーションを EF Core 2.x にアップグレードする](xref:core/miscellaneous/1x-2x-upgrade)**</span><span class="sxs-lookup"><span data-stu-id="0b47b-110">**[Upgrading existing applications to EF Core 2.x](xref:core/miscellaneous/1x-2x-upgrade)**</span></span>


## <a name="get-entity-framework-core"></a><span data-ttu-id="0b47b-111">Entity Framework Core を入手する</span><span class="sxs-lookup"><span data-stu-id="0b47b-111">Get Entity Framework Core</span></span>

<span data-ttu-id="0b47b-112">使用するデータベース プロバイダーに対して [NuGet パッケージ](https://docs.nuget.org/ndocs/quickstart/use-a-package)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="0b47b-112">[Install the NuGet package](https://docs.nuget.org/ndocs/quickstart/use-a-package) for the database provider you want to use.</span></span> <span data-ttu-id="0b47b-113">たとえば、</span><span class="sxs-lookup"><span data-stu-id="0b47b-113">E.g.</span></span> <span data-ttu-id="0b47b-114">クロスプラットフォーム開発で SQL Server プロバイダーをインストールするためにコマンド ラインで `dotnet` ツールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0b47b-114">to install the SQL Server provider in cross-platform development using `dotnet` tool in the command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="0b47b-115">あるいは、Visual Studio でパッケージ マネージャー コンソールを使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0b47b-115">Or in Visual Studio, using the Package Manager Console:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
<span data-ttu-id="0b47b-116">利用できるプロバイダーについては「[Database Providers](providers/index.md)」 (データベース プロバイダー) を、インストール手順の詳細については「[Installing EF Core](get-started/install/index.md)」 (EF Core のインストール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-116">See [Database Providers](providers/index.md) for information on available providers and [Installing EF Core](get-started/install/index.md) for more detailed installation steps.</span></span>

## <a name="the-model"></a><span data-ttu-id="0b47b-117">モデル</span><span class="sxs-lookup"><span data-stu-id="0b47b-117">The Model</span></span>

<span data-ttu-id="0b47b-118">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b47b-118">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="0b47b-119">モデルはエンティティ クラスと、データベースとのセッションを表す、派生コンテキストから構成されます。このセッションにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="0b47b-119">A model is made up of entity classes and a derived context that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="0b47b-120">詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-120">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="0b47b-121">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、EF 移行を利用してモデルからデータベースを作成したり (モデルが時間の経過と共に変化するのに合わせ、進化させたり) できます。</span><span class="sxs-lookup"><span data-stu-id="0b47b-121">You can generate a model from an existing database, hand code a model to match your database, or use EF Migrations to create a database from your model (and evolve it as your model changes over time).</span></span>

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

## <a name="querying"></a><span data-ttu-id="0b47b-122">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="0b47b-122">Querying</span></span>

<span data-ttu-id="0b47b-123">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="0b47b-123">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="0b47b-124">詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-124">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="0b47b-125">データの保存</span><span class="sxs-lookup"><span data-stu-id="0b47b-125">Saving Data</span></span>

<span data-ttu-id="0b47b-126">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="0b47b-126">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="0b47b-127">詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b47b-127">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

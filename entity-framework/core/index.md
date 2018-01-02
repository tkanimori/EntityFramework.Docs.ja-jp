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
# <a name="entity-framework-core-quick-overview"></a>Entity Framework Core 概要

Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、プラットフォームに依存しません。

EF Core はオブジェクト リレーショナル マッパー (O/RM) であり、.NET 開発者は .NET オブジェクトを利用してデータベースを操作できます。 開発者が通常記述しなければならないデータアクセス コードの多くが不要になります。 EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。

コードを記述しながら学習したいのであれば、EF Core [入門](get-started/index.md)ガイドのご利用をお勧めします。

## <a name="latest-version-ef-core-20"></a>最新版: EF Core 2.0

EF Core に精通しており、新しいバージョンの詳細をすぐに知りたい場合は、以下を参照してください。

- **[EF Core 2.0 の新機能](what-is-new/index.md)**
- **[既存アプリケーションを EF Core 2.0 にアップグレードする](miscellaneous/1x-2x-upgrade.md)**

## <a name="get-entity-framework-core"></a>Entity Framework Core を入手する

使用するデータベース プロバイダーに対して [NuGet パッケージ](https://docs.nuget.org/ndocs/quickstart/use-a-package)をインストールします。 例: クロスプラットフォーム開発で SQL Server プロバイダーをインストールするためにコマンド ラインで `dotnet` ツールを使用する場合、次のようになります。

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

あるいは、Visual Studio でパッケージ マネージャー コンソールを使用する場合、次のようになります。

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
利用できるプロバイダーについては「[Database Providers](providers/index.md)」 (データベース プロバイダー) を、インストール手順の詳細については「[Installing EF Core](get-started/install/index.md)」 (EF Core のインストール) を参照してください。

## <a name="the-model"></a>モデル

EF Core では、データ アクセスはモデルを利用して実行されます。 モデルはエンティティ クラスと、データベースとのセッションを表す、派生コンテキストから構成されます。このセッションにより、データのクエリと保存が可能になります。 詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。

既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、EF 移行を利用してモデルからデータベースを作成したり (モデルが時間の経過と共に変化するのに合わせ、進化させたり) できます。

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

## <a name="querying"></a>クエリ実行

エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。 詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a>データの保存

データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。 詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

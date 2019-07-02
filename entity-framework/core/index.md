---
title: 概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 0e35a2b3f89c92b717b8e05c8fa3ae5af5ce8fd3
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333780"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。

EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。

EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。

## <a name="the-model"></a>モデル

EF Core では、データ アクセスはモデルを利用して実行されます。 モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。これにより、データのクエリと保存が可能になります。 詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。

既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、[EF 移行](managing-schemas/migrations/index.md)を使ってモデルからデータベースを作成してから、モデルが時間の経過と共に変化するのに合わせてそれを進化させたりできます。

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

## <a name="next-steps"></a>次の手順

入門チュートリアルについては、「[Entity Framework Core の概要](get-started/index.md)」を参照してください。


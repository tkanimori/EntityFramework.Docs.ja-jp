---
title: .NET Framework - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 39e77ab8c124df67458cc5fa6db2882b65943ebe
ms.sourcegitcommit: 4467032fd6ca223e5965b59912d74cf88a1dd77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39388469"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a>既存のデータベースを使用した .NET Framework での EF Core の概要

このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。 既存のデータベースに基づく Entity Framework モデルを作成するために、リバース エンジニアリングを使用します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)は GitHub で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するには、次の条件を満たす必要があります。

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) - バージョン 15.3 以上

* [最新バージョンの NuGet パッケージ マネージャー](https://dist.nuget.org/index.html)

* [最新バージョンの Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

* [Blogging データベース](#blogging-database)

### <a name="blogging-database"></a>Blogging データベース

このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。

> [!TIP]  
> **Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略できます。

* Visual Studio を開く

* [ツール] > [データベースへの接続...]

* **[Microsoft SQL Server]** を選択して **[続行]** をクリックします。

* **[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。

* **[データベース名]** に「**master**」を入力して **[OK]** をクリックします。

* **[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。

* **[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。

* 以下に表示されているスクリプトを、クエリ エディターにコピーします

* クエリ エディターを右クリックし、**[実行]** を選択します。

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio を開く

* [ファイル] > [新規] > [プロジェクト...]

* 左側のメニューから [テンプレート] > [Visual C#] > [Windows] の順に選択します

* **[コンソール アプリケーション]** プロジェクト テンプレートを選択します。

* **.NET Framework 4.6.1** 以降を対象としていることを確認します。

* プロジェクト名を設定し、**[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

既存のデータベースのリバース エンジニアリングを有効にするには、他にも 2, 3 個のパッケージをインストールする必要があります。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

## <a name="reverse-engineer-your-model"></a>モデルのリバース エンジニアリングを行う

ここで、お使いの既存のデータベースに基づいて、EF モデルを作成します。

* [ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]

* 既存のデータベースからモデルを作成するには、次のコマンドを実行します。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

リバース エンジニアリングのプロセスでは、エンティティ クラスと、既存のデータベースのスキーマに基づく派生コンテキストを作成しました。 エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)] -->
``` csharp
using System;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class Blog
    {
        public Blog()
        {
            Post = new HashSet<Post>();
        }

        public int BlogId { get; set; }
        public string Url { get; set; }

        public virtual ICollection<Post> Post { get; set; }
    }
}
```

コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class BloggingContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>(entity =>
            {
                entity.Property(e => e.Url).IsRequired();
            });

            modelBuilder.Entity<Post>(entity =>
            {
                entity.HasOne(d => d.Blog)
                    .WithMany(p => p.Post)
                    .HasForeignKey(d => d.BlogId);
            });
        }

        public virtual DbSet<Blog> Blog { get; set; }
        public virtual DbSet<Post> Post { get; set; }
    }
}
```

## <a name="use-your-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *Program.cs* を開きます。

* このファイルの内容を次のコードに置き換えます。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blog.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blog)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* [デバッグ] > [デバッグなしで開始]

1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。

![イメージ](_static/output-existing-db.png)

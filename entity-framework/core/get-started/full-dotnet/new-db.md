---
title: ".NET Framework - 新しいデータベース - EF Core の概要"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: bd7054c6834ae11bfdc352d63654e4304771e432
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a>新しいデータベースを使用した .NET Framework での EF Core の概要

このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。 モデルからの移行によってデータベースを作成します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)は GitHub で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するには、次の条件を満たす必要があります。

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)

* [最新バージョンの NuGet パッケージ マネージャー](https://dist.nuget.org/index.html)

* [最新バージョンの Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio を開く

* [ファイル] > [新規] > [プロジェクト...]

* 左側のメニューから、[テンプレート] > [Visual C#] > [Windows クラシック デスクトップ] の順に選択します。

* **[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。

* **.NET Framework 4.5.1** 以降を対象としていることを確認します。

* プロジェクト名を設定し、**[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。 そこで、そのツールのパッケージもインストールします。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

## <a name="create-your-model"></a>モデルを作成する

ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。

* [プロジェクト] > [クラスの追加...]

* 名前に「*Model.cs*」を入力して **[OK]** をクリックします。

* このファイルの内容を次のコードに置き換えます。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;");
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

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

> [!TIP]  
> 実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は `App.Config` ファイルにそれぞれ記述し、`ConfigurationManager` を使用して読み取ります。 わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのコード ファイルに記述しています。

## <a name="create-your-database"></a>データベースを作成する

これでモデルができたので、移行を利用して自分用のデータベースを作成できます。

* [ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]

* `Add-Migration MyFirstMigration` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。

* `Update-Database` を実行して、新しい移行をデータベースに適用します。 データベースがまだ存在しないため、作成されてから移行が適用されます。

> [!TIP]  
> モデルをさらに変更する場合、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応するスキーマの変更をデータベースに適用します。 スキャフォールディング コードを確認 (および必要な変更) したら、`Update-Database` コマンドを使用してデータベースに変更を適用できます。
>
>EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。

## <a name="use-your-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *Program.cs* を開きます。

* このファイルの内容を次のコードに置き換えます。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blogs)
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

![image](_static/output-new-db.png)

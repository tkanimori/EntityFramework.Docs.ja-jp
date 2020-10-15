---
title: 非同期クエリと保存-EF6
description: Entity Framework 6 での非同期クエリと保存
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 2b5f6f868cbf2e0699a943cf68c8568550f4ba36
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063401"
---
# <a name="async-query-and-save"></a>非同期クエリと保存
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 では、.NET 4.5 で導入された [async キーワードと await キーワード](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) を使用して、非同期クエリと保存のサポートが導入されました。 すべてのアプリケーションが非同期性の恩恵を受けるとは限りませんが、長時間実行、ネットワーク、i/o バインドのタスクを処理する際に、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。

## <a name="when-to-really-use-async"></a>Async を実際に使用するタイミング

このチュートリアルの目的は、非同期の概念を紹介することです。非同期の概念を使用すると、非同期および同期のプログラム実行の違いを簡単に観察できます。 このチュートリアルは、非同期プログラミングで利点が得られる主要なシナリオを示すことを目的としていません。

非同期プログラミングは、マネージスレッドからのコンピューティング時間を必要としない操作を待機している間に、他の作業を行うために、現在のマネージスレッド (.NET コードを実行しているスレッド) を解放することに主に焦点を合わせています。 たとえば、データベースエンジンがクエリを処理している間は、.NET コードによって実行されるものはありません。

クライアントアプリケーション (WinForms、WPF など) では、非同期操作の実行中に UI の応答性を維持するために現在のスレッドを使用できます。 サーバーアプリケーション (ASP.NET など) では、スレッドを使用して他の受信要求を処理できます。これにより、メモリ使用量を削減したり、サーバーのスループットを向上させることができます。

非同期を使用するほとんどのアプリケーションでは、顕著な利点はなく、悪影響を及ぼす可能性もあります。 テスト、プロファイル、および一般的な意味を使用して、特定のシナリオでの非同期の影響を測定してからコミットします。

非同期の詳細については、次のリソースを参照してください。

-   [Brandon Bray の .NET 4.5 の async/await の概要](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   MSDN ライブラリの[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)ページ
-   [Async を使用して ASP.NET Web アプリケーションを構築する方法](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (サーバースループットの増加に関するデモを含む)

## <a name="create-the-model"></a>モデルを作成する

ここでは、 [Code First ワークフロー](xref:ef6/modeling/code-first/workflows/new-database) を使用してモデルを作成し、データベースを生成します。ただし、非同期機能は ef デザイナーで作成されたものを含むすべての ef モデルで動作します。

-   コンソールアプリケーションを作成し、 **Asyncdemo**を呼び出す
-   EntityFramework NuGet パッケージを追加する
    -   ソリューションエクスプローラーで、 **Asyncdemo** プロジェクトを右クリックします。
    -   [ **NuGet パッケージの管理...** ] を選択します。
    -   [NuGet パッケージの管理] ダイアログで、[ **オンライン** ] タブを選択し、 **entityframework** パッケージを選択します。
    -   **[Install]** (インストール) をクリックします。
-   次の実装を使用して **Model.cs** クラスを追加します。

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a>同期プログラムを作成する

EF モデルが完成したので、いくつかのデータアクセスを実行するために使用するコードを記述してみましょう。

-   **Program.cs**の内容を次のコードに置き換えます。

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

このコードは、新しい**ブログ**をデータベースに保存し、データベースからすべての**ブログ**を取得して**コンソール**に出力する、実行**databaseoperations**メソッドを呼び出します。 その後、プログラムはその日の引用符を **コンソール**に書き込みます。

コードは同期的であるため、プログラムを実行したときに次の実行フローを観察できます。

1.  **SaveChanges** は、データベースへの新しい **ブログ** のプッシュを開始します。
2.  **SaveChanges** の完了
3.  すべての **ブログ** のクエリがデータベースに送信されます
4.  クエリが返され、結果が**コンソール**に書き込まれます。
5.  日の引用が**コンソール**に書き込まれます

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>非同期にする

プログラムを起動して実行したので、新しい async キーワードと await キーワードの使用を開始できます。 Program.cs には次の変更が加えられました。

1.  行 2: system.string 名前空間の using ステートメントにより、EF async 拡張メソッドにアクセス**できます。**
2.  行 4: system.string 名前空間の using ステートメントでは、**タスク**の種類を使用**できます。**
3.  12行目 & 18: 実行の進行状況を監視するタスクとしてキャプチャしています (12 行目)。プログラムの **すべての作業** が完了したら、このタスクのプログラム実行をブロックします (18 行目)。
4.  行 25:**非同期**としてマークされ、**タスク**が返されるように、**データベース操作**を更新しました。
5.  行 35: 現在、SaveChanges の非同期バージョンを呼び出して、完了を待機しています。
6.  行 42: 現在、非同期バージョンの ToList を呼び出し、結果を待機しています。

System.string 名前空間の使用可能な拡張メソッドの包括的な一覧については、QueryableExtensions クラスを参照してください。 *また、using ステートメントに "using system.string" を追加する必要もあります。*

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

コードが非同期になったので、プログラムの実行時に別の実行フローを観察できます。

1. **SaveChanges** は、データベースへの新しい **ブログ** のプッシュを開始します。  
    *コマンドがデータベースに送信されると、現在のマネージスレッドでは、それ以上のコンピューティング時間は必要ありません。実行が完了していなくても、このメソッドはを返します。 Main メソッドのプログラムフローは続行 **されます** 。*
2. **日の引用がコンソールに書き込まれます**  
    *Main メソッドで実行する作業がなくなったため、データベース操作が完了するまで、待機呼び出しでマネージスレッドがブロックされます。完了すると、残りの実行 **データベース操作** が実行されます。*
3.  **SaveChanges** の完了  
4.  すべての **ブログ** のクエリがデータベースに送信されます  
    *この場合も、データベースでクエリが処理されている間は、マネージスレッドで他の作業を自由に行うことができます。他のすべての実行が完了したため、スレッドは待機呼び出しを停止します。*
5.  クエリが返され、結果が**コンソール**に書き込まれます。  

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>通じ重要

EF の非同期メソッドを簡単に使用できるようになりました。 非同期の利点は、単純なコンソールアプリでは明らかでない場合がありますが、実行時間が長い場合やネットワークにバインドされているアクティビティによってアプリケーションがブロックされる可能性がある場合や、大量のスレッドによってメモリフットプリントが増加する場合にも、これらの同じ戦略を適用できます。

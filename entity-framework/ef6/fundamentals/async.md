---
title: 非同期クエリと保存-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: bf2039110962e8dd114242dcd0b9454963750774
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306593"
---
# <a name="async-query-and-save"></a>非同期クエリと保存
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 では、.NET 4.5 で導入された[async キーワードと await キーワード](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)を使用して、非同期クエリと保存のサポートが導入されました。 すべてのアプリケーションが非同期性の恩恵を受けるとは限りませんが、長時間実行、ネットワーク、i/o バインドのタスクを処理する際に、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。

## <a name="when-to-really-use-async"></a>Async を実際に使用するタイミング

このチュートリアルの目的は、非同期の概念を紹介することです。非同期の概念を使用すると、非同期および同期のプログラム実行の違いを簡単に観察できます。 このチュートリアルは、非同期プログラミングで利点が得られる主要なシナリオを示すことを目的としていません。

非同期プログラミングは、マネージスレッドからのコンピューティング時間を必要としない操作を待機している間に、他の作業を行うために、現在のマネージスレッド (.NET コードを実行しているスレッド) を解放することに主に焦点を合わせています。 たとえば、データベースエンジンがクエリを処理している間は、.NET コードによって実行されるものはありません。

クライアントアプリケーション (WinForms、WPF など) では、非同期操作の実行中に UI の応答性を維持するために現在のスレッドを使用できます。 サーバーアプリケーション (ASP.NET など) では、スレッドを使用して他の受信要求を処理できます。これにより、メモリ使用量を削減したり、サーバーのスループットを向上させることができます。

非同期を使用するほとんどのアプリケーションでは、顕著な利点はなく、悪影響を及ぼす可能性もあります。 テスト、プロファイル、および一般的な意味を使用して、特定のシナリオでの非同期の影響を測定してからコミットします。

非同期の詳細については、次のリソースを参照してください。

-   [Brandon Bray の .NET 4.5 の async/await の概要](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   MSDN ライブラリの[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)ページ
-   [Async を使用して ASP.NET Web アプリケーションを構築する方法](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(サーバースループットの増加に関するデモが含まれています)

## <a name="create-the-model"></a>モデルを作成する

ここでは、 [Code First ワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)を使用してモデルを作成し、データベースを生成します。ただし、非同期機能は ef デザイナーで作成されたものを含むすべての ef モデルで動作します。

-   コンソールアプリケーションを作成し、 **Asyncdemo**を呼び出す
-   EntityFramework NuGet パッケージを追加する
    -   ソリューションエクスプローラーで、 **Asyncdemo**プロジェクトを右クリックします。
    -   **[NuGet パッケージの管理...]** を選択します。
    -   NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。
    -   **[インストール]** をクリックします。
-   次の実装を使用して**Model.cs**クラスを追加します。

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

このコードは、新しい**ブログ**をデータベースに保存し、データベースからすべての**ブログ**を取得して**コンソール**に出力する、実行**databaseoperations**メソッドを呼び出します。 その後、プログラムはその日の引用符を**コンソール**に書き込みます。

コードは同期的であるため、プログラムを実行したときに次の実行フローを観察できます。

1.  **SaveChanges**は、データベースへの新しい**ブログ**のプッシュを開始します。
2.  **SaveChanges**の完了
3.  すべての**ブログ**のクエリがデータベースに送信されます
4.  クエリが返され、結果が**コンソール**に書き込まれます。
5.  日の引用が**コンソール**に書き込まれます

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>非同期にする

プログラムを起動して実行したので、新しい async キーワードと await キーワードの使用を開始できます。 Program.cs には次の変更が加えられました。

1.  2行目:System.string**名前空間**の using ステートメントを使用すると、EF async 拡張メソッドにアクセスできます。
2.  4行目:System.string**名前空間**の using ステートメントを使用すると、**タスク**の種類を使用できます。
3.  12行目 & 18:実行**の進行**状況を監視し、プログラムのすべての作業が完了した後で、プログラムの実行をブロックするタスクとしてキャプチャしています (18 行目)。
4.  25行目:**非同期**としてマークされ、**タスク**が返されるように、**データベース操作**を更新しました。
5.  35行目:現在、SaveChanges の非同期バージョンを呼び出して、完了を待機しています。
6.  42行目:これで、非同期バージョンの ToList を呼び出し、結果を待機しています。

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

1.  コマンドがデータベースに送信されると、SaveChanges *によってデータベースへの新しい**ブログ**のプッシュが開始されます。現在のマネージスレッドでは、それ以上のコンピューティング時間は必要ありません。実行が完了していなくて**も、この**メソッドはを返します。 Main メソッドのプログラムフローは続行されます。*
2.  
    Main メソッドで実行する作業がなくなったため、その日の見積もりはコンソール*に書き込まれます。そのため、データベース操作が完了するまで、待機呼び出しでマネージスレッドがブロックされます。完了すると、残りの実行**データベース操作**が実行されます。*
3.  **SaveChanges**の完了
4.  すべての**ブログ**のクエリが再度データベース*に送信されます。マネージスレッドは、データベースでクエリが処理されている間に、他の作業を自由に行うことができます。他のすべての実行が完了したため、スレッドは待機呼び出しを停止します。*
5.  クエリが返され、結果が**コンソール**に書き込まれます。

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>通じ重要

EF の非同期メソッドを簡単に使用できるようになりました。 非同期の利点は、単純なコンソールアプリでは明らかでない場合がありますが、実行時間の長いまたはネットワークにバインドされたアクティビティによってアプリケーションがブロックされる可能性がある場合や、多数のスレッドがメモリフットプリントを増やします。

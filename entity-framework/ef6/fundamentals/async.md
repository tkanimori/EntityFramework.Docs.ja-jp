---
title: 非同期クエリを実行し、保存、EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
caps.latest.revision: 3
ms.openlocfilehash: 655676029b3a4e42bbe257ff6091179930b1814e
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122556"
---
# <a name="async-query-and-save"></a>非同期クエリを実行し、保存
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 に非同期クエリを使用して保存のサポートが導入された、 [async と await キーワード](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).NET 4.5 で導入されました。 すべてのアプリケーションの非同期処理にとって有益な場合がありますは、実行時間の長い、ネットワークまたは O バインドのタスクを処理するときに、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。

## <a name="when-to-really-use-async"></a>本当に async を使用する場合

このチュートリアルの目的は、非同期および同期プログラムの実行の違いを観察する簡単な方法で非同期の概念を導入します。 このチュートリアルものではありません、主なシナリオのいずれかを示すために非同期プログラミングがメリットを提供します。

非同期プログラミングは、マネージ スレッドから任意のコンピューティング時間を必要としない操作を待っている間に他の作業の現在のマネージ スレッド (スレッド実行 .NET コード) を解放する方法に主がいます。 たとえば、データベース エンジン クエリの処理は、何もない .NET コードによって実行します。

クライアント アプリケーション (WinForms、WPF など) では、非同期操作の実行中に、UI の応答性を維持する、現在のスレッドを使用できます。 -他の受信要求を処理するスレッドを使用できるサーバー アプリケーション (ASP.NET など) では、メモリ使用量を削減したり、サーバーのスループットを向上したりこのことができます。

Async を使用するほとんどのアプリケーションではありません顕著なメリットと悪影響もする可能性があります。 コミットするのに前に特定のシナリオにおける非同期の影響を測定するのにには、テスト、プロファイリング、および常識を使用します。

非同期の詳細についていくつかその他のリソースを次に示します。

-   [.NET 4.5 で非同期/待機の Brandon Bray の概要](http://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   [非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)MSDN ライブラリ内のページ
-   [ビルド ASP.NET Web アプリケーションを使用して非同期の追加方法](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(増加のサーバーのスループットのデモが含まれています)

## <a name="create-the-model"></a>モデルを作成する

使用する、 [Code First ワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)モデルを作成し、非同期の機能は EF Designer で作成されたものを含むすべての EF モデルの動作が、データベースを生成します。

-   コンソール アプリケーションを作成し、それを呼び出す**AsyncDemo**
-   EntityFramework NuGet パッケージを追加します。
    -   ソリューション エクスプ ローラーを右クリックし、 **AsyncDemo**プロジェクト
    -   選択**NuGet パッケージを管理しています.**
    -   NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ
    -   クリックして**インストール**
-   追加、 **Model.cs**次の実装クラス

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

 

## <a name="create-a-synchronous-program"></a>同期プログラムを作成します。

EF モデルをしたら、それを使用していくつかのデータ アクセスを実行するいくつかのコードを記述してみましょう。

-   内容を置き換える**Program.cs**を次のコード

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

                Console.WriteLine();
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
                    db.SaveChanges();

                    // Query for all blogs ordered by name
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine();
                    Console.WriteLine("All blogs:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

このコードは、 **PerformDatabaseOperations**新しい保存メソッド**ブログ**データベースを取得してすべてを**ブログ**データベースからに出力します、**コンソール**します。 その後、プログラムが 1 日の見積もりを書き込みます、**コンソール**します。

コードは、同期であるため、プログラムを実行すると、次の実行フローを確認しましたできます。

1.  **SaveChanges**新しいプッシュを開始**ブログ**データベース
2.  **SaveChanges**が完了します。
3.  すべてのクエリ**ブログ**データベースに送信されます
4.  クエリを返しする結果が書き込まれる**コンソール**
5.  1 日の見積もりが書き込む**コンソール**

![SyncOutput](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>非同期になります

このプログラムを起動して実行したら、新しい非同期の使用と、await キーワードを開始できます。 Program.cs に、次の変更を行いました

1.  2 行目: を使用して、ステートメント、 **System.Data.Entity**名前空間に対するアクセスを提供、EF の非同期拡張メソッド。
2.  行 4: を使用して、ステートメント、 **System.Threading.Tasks**名前空間では、使用できる、**タスク**型。
3.  12 & 18: に関しての進行状況を監視するタスクとしてキャプチャされる**PerformSomeDatabaseOperations** (12 行目) し、このプログラムの実行をブロックし、タスク完了 1 回にすべての作業、プログラムが (行 18) に行われます。
4.  25 行目: 更新プログラムをしました**PerformSomeDatabaseOperations**としてマーク済みである**async**戻って、**タスク**します。
5.  行 35: 今すぐ SaveChanges の非同期バージョンの呼び出ししていますの完了を待機しています。
6.  行 42: 今すぐ ToList の非同期バージョンを呼び出すしています、結果を待機しています。

System.Data.Entity 名前空間の使用可能な拡張メソッドの包括的な一覧、QueryableExtensions クラスを参照してください。 *またを使用する「System.Data.Entity を使用して」を追加する必要がありますステートメント。*

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

これで、コードが、非同期、プログラムを実行するとさまざまな実行フローを観察したことができます。

1.  **SaveChanges**新しいプッシュを開始**ブログ**データベースに*より高いの計算に時間が現在のマネージ スレッドで必要ありません、データベースにコマンドが送信されるとします。**PerformDatabaseOperations**メソッドを返します (これは実行が完了していない) 場合でも、Main メソッドで、プログラム フローが続行されます。*
2.  **1 日の見積もりがコンソールに書き込まれる**
    *待機にマネージ スレッドがブロックされている他の作業は、Main メソッドであるため、データベース操作が完了するまでを呼び出します。これが完了するの残りの部分、 **PerformDatabaseOperations** *が実行されます。
3.  **SaveChanges**が完了します。
4.  すべてのクエリ**ブログ**データベースに送信される*ここでも、マネージ スレッドは無料で、クエリは、データベースの処理中に他の作業を行います。他のすべての実行が完了するため、スレッドはだけ停止待機の呼び出しでただしします。*
5.  クエリを返しする結果が書き込まれる**コンソール**

![AsyncOutput](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>したがって、

ここではさせる簡単な方法を説明しましたの EF の非同期メソッドを使用します。 状況アクティビティの実行時間の長いまたはネットワークにバインドされる可能性がありますそれ以外の場合、アプリケーションをブロックするスレッドの数が多い原因でこれらと同じ戦略を適用できますが非同期の利点は簡単なコンソール アプリで非常に明らかにできない、メモリ使用量を増やします。

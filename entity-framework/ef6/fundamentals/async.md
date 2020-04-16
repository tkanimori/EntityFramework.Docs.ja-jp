---
title: 非同期クエリと保存 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434241"
---
# <a name="async-query-and-save"></a>非同期クエリと保存
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 では、非同期クエリのサポートが導入され、.NET 4.5 で導入された[非同期キーワードと await キーワードを](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)使用して保存されました。 すべてのアプリケーションが非同期の恩恵を受けるわけではありませんが、長時間実行されるネットワークまたは I/O バインドタスクを処理する際に、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。

## <a name="when-to-really-use-async"></a>非同期を実際に使用するタイミング

このチュートリアルの目的は、非同期プログラム実行と同期プログラム実行の違いを簡単に確認できるように非同期の概念を紹介することです。 このチュートリアルでは、非同期プログラミングで利点が得られる主要なシナリオを説明するためのものではありません。

非同期プログラミングは、マネージ スレッドからの計算時間を必要としない操作を待機している間、現在のマネージ スレッド (.NET コードを実行しているスレッド) を解放して他の処理を行うことを主に重点的に行います。 たとえば、データベース エンジンがクエリを処理している間は、.NET コードで何も行う必要はありません。

クライアント アプリケーション (WinForms、WPF など) では、非同期操作の実行中に UI の応答性を維持するために現在のスレッドを使用できます。 サーバーアプリケーション(ASP.NETなど)では、スレッドを使用して他の着信要求を処理できます。

非同期を使用するほとんどのアプリケーションでは、顕著な利点はなく、有害である可能性さえあります。 テスト、プロファイリング、および常識を使用して、特定のシナリオにおける非同期の影響を評価してから、テストにコミットします。

非同期について学ぶリソースを次に示します。

-   [ブランドン・ブレイの非同期/待機の概要は.NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   MSDN ライブラリの[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)ページ
-   [非同期を使用してASP.NET Web アプリケーションを構築する方法](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(サーバー スループットの向上のデモを含む)

## <a name="create-the-model"></a>モデルを作成する

[コードファーストワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)を使用してモデルを作成し、データベースを生成しますが、非同期機能は EF Designer で作成されたものを含むすべての EF モデルで動作します。

-   コンソール アプリケーションを作成し、**それを AsyncDemo**と呼びます
-   エンティティ フレームワーク の NuGet パッケージを追加します。
    -   ソリューション エクスプローラーで **、AsyncDemo**プロジェクトを右クリックします。
    -   **[NuGet パッケージの管理] を選択します。**
    -   [NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し **、EntityFramework**パッケージを選択します。
    -   [インストール] をクリック**します。**
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

 

## <a name="create-a-synchronous-program"></a>同期プログラムの作成

EF モデルができたので、それを使用してデータ アクセスを実行するコードを記述しましょう。

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

このコードは、新しい**ブログ**をデータベースに保存し、データベースからすべての**ブログ**を取得し、**コンソール**に出力する**PerformDatabaseOperations**メソッドを呼び出します。 この後、プログラムはコンソールに一日の引用を書き込**みます**。

コードは同期的であるため、プログラムを実行するときに次の実行フローを確認できます。

1.  **保存変更は**、データベースに新しい**ブログ**をプッシュし始めます
2.  **保存変更が**完了しました
3.  すべての**ブログの**クエリがデータベースに送信されます
4.  クエリの戻り値と結果が**コンソール**に書き込まれます
5.  その日の引用はコンソールに書き込**まれます**

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>非同期にする

プログラムを起動して実行したので、新しい非同期キーワードと await キーワードの使用を開始できます。 Program.csに次の変更を加えました

1.  2 行目: **System.Data.Entity**名前空間の using ステートメントは、EF 非同期拡張メソッドへのアクセスを提供します。
2.  行 4: **System.Threading.Tasks**名前空間の using ステートメントを使用すると、**タスク**の種類を使用できます。
3.  12 行 & 18: **PerformSomeDatabaseOperations** (12 行目) の進行状況を監視するタスクとしてキャプチャし、プログラムのすべての作業が完了したらこのタスクを実行するプログラムの実行をブロックします (行 18)。
4.  25 行目: 非同期としてマークされ、**タスク**を返すように **、PerformSome データベース操作****を**更新しました。
5.  35 行目: 非同期バージョンの SaveChanges を呼び出して、完了を待っています。
6.  42 行目: 非同期バージョンの ToList を呼び出して、結果を待っています。

名前空間で使用できる拡張メソッドの包括的な一覧については、クラスを参照してください。 *また、using ステートメントに "System.Data.Entity を使用する" を追加する必要もあります。*

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

コードが非同期になったので、プログラムを実行するときに別の実行フローを観察できます。

1. **保存変更は**、データベースに新しい**ブログ**をプッシュし始めます  
    *コマンドがデータベースに送信されると、現在のマネージ スレッドでこれ以上の計算時間は必要ありません。メソッド**は**(実行が完了していない場合でも)返し、Main メソッドのプログラム フローが続行されます。*
2. **その日の引用はコンソールに書き込まれます**  
    *Main メソッドで実行する処理がこれ以上存在しないため、データベース操作が完了するまで、待機呼び出しでマネージ スレッドがブロックされます。完了すると、データベース**の操作**の残りの部分が実行されます。*
3.  **保存変更が**完了しました  
4.  すべての**ブログの**クエリがデータベースに送信されます  
    *マネージ スレッドは、クエリがデータベースで処理されている間は、他の処理を自由に行うことができます。他のすべての実行が完了したので、スレッドは Wait 呼び出しで停止するだけです。*
5.  クエリの戻り値と結果が**コンソール**に書き込まれます  

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>テイクアウト

EF の非同期メソッドを使いやすい点を見ていった。 非同期の利点は単純なコンソール アプリでは明らかではないかもしれませんが、長時間実行またはネットワーク バインドアクティビティがアプリケーションをブロックしたり、大量のスレッドがメモリの占有領域を増やす場合に、これらの同じ戦略を適用できます。

---
title: 非同期クエリを実行し、保存、EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: 4ed4f5c13341f33ccff8325a5ddacd8f7b195a76
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283824"
---
# <a name="async-query-and-save"></a><span data-ttu-id="f4040-102">非同期クエリを実行し、保存</span><span class="sxs-lookup"><span data-stu-id="f4040-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="f4040-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f4040-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="f4040-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="f4040-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="f4040-105">EF6 に非同期クエリを使用して保存のサポートが導入された、 [async と await キーワード](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).NET 4.5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f4040-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="f4040-106">すべてのアプリケーションの非同期処理にとって有益な場合がありますは、実行時間の長い、ネットワークまたは O バインドのタスクを処理するときに、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4040-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="f4040-107">本当に async を使用する場合</span><span class="sxs-lookup"><span data-stu-id="f4040-107">When to really use async</span></span>

<span data-ttu-id="f4040-108">このチュートリアルの目的は、非同期および同期プログラムの実行の違いを観察する簡単な方法で非同期の概念を導入します。</span><span class="sxs-lookup"><span data-stu-id="f4040-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="f4040-109">このチュートリアルものではありません、主なシナリオのいずれかを示すために非同期プログラミングがメリットを提供します。</span><span class="sxs-lookup"><span data-stu-id="f4040-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="f4040-110">非同期プログラミングは、マネージ スレッドから任意のコンピューティング時間を必要としない操作を待っている間に他の作業の現在のマネージ スレッド (スレッド実行 .NET コード) を解放する方法に主がいます。</span><span class="sxs-lookup"><span data-stu-id="f4040-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="f4040-111">たとえば、データベース エンジン クエリの処理は、何もない .NET コードによって実行します。</span><span class="sxs-lookup"><span data-stu-id="f4040-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="f4040-112">クライアント アプリケーション (WinForms、WPF など) では、非同期操作の実行中に、UI の応答性を維持する、現在のスレッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4040-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="f4040-113">-他の受信要求を処理するスレッドを使用できるサーバー アプリケーション (ASP.NET など) では、メモリ使用量を削減したり、サーバーのスループットを向上したりこのことができます。</span><span class="sxs-lookup"><span data-stu-id="f4040-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="f4040-114">Async を使用するほとんどのアプリケーションではありません顕著なメリットと悪影響もする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4040-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="f4040-115">コミットするのに前に特定のシナリオにおける非同期の影響を測定するのにには、テスト、プロファイリング、および常識を使用します。</span><span class="sxs-lookup"><span data-stu-id="f4040-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="f4040-116">非同期の詳細についていくつかその他のリソースを次に示します。</span><span class="sxs-lookup"><span data-stu-id="f4040-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="f4040-117">.NET 4.5 で非同期/待機の Brandon Bray の概要</span><span class="sxs-lookup"><span data-stu-id="f4040-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="f4040-118">[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)MSDN ライブラリ内のページ</span><span class="sxs-lookup"><span data-stu-id="f4040-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="f4040-119">[ビルド ASP.NET Web アプリケーションを使用して非同期の追加方法](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(増加のサーバーのスループットのデモが含まれています)</span><span class="sxs-lookup"><span data-stu-id="f4040-119">[How to Build ASP.NET Web Applications Using Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="f4040-120">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="f4040-120">Create the model</span></span>

<span data-ttu-id="f4040-121">使用する、 [Code First ワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)モデルを作成し、非同期の機能は EF Designer で作成されたものを含むすべての EF モデルの動作が、データベースを生成します。</span><span class="sxs-lookup"><span data-stu-id="f4040-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="f4040-122">コンソール アプリケーションを作成し、それを呼び出す**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="f4040-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="f4040-123">EntityFramework NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="f4040-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="f4040-124">ソリューション エクスプ ローラーを右クリックし、 **AsyncDemo**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="f4040-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="f4040-125">選択**NuGet パッケージを管理しています.**</span><span class="sxs-lookup"><span data-stu-id="f4040-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="f4040-126">NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ</span><span class="sxs-lookup"><span data-stu-id="f4040-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="f4040-127">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="f4040-127">Click **Install**</span></span>
-   <span data-ttu-id="f4040-128">追加、 **Model.cs**次の実装クラス</span><span class="sxs-lookup"><span data-stu-id="f4040-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="f4040-129">同期プログラムを作成します。</span><span class="sxs-lookup"><span data-stu-id="f4040-129">Create a synchronous program</span></span>

<span data-ttu-id="f4040-130">EF モデルをしたら、それを使用していくつかのデータ アクセスを実行するいくつかのコードを記述してみましょう。</span><span class="sxs-lookup"><span data-stu-id="f4040-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="f4040-131">内容を置き換える**Program.cs**を次のコード</span><span class="sxs-lookup"><span data-stu-id="f4040-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="f4040-132">このコードは、 **PerformDatabaseOperations**新しい保存メソッド**ブログ**データベースを取得してすべてを**ブログ**データベースからに出力します、**コンソール**します。</span><span class="sxs-lookup"><span data-stu-id="f4040-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="f4040-133">その後、プログラムが 1 日の見積もりを書き込みます、**コンソール**します。</span><span class="sxs-lookup"><span data-stu-id="f4040-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="f4040-134">コードは、同期であるため、プログラムを実行すると、次の実行フローを確認しましたできます。</span><span class="sxs-lookup"><span data-stu-id="f4040-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="f4040-135">**SaveChanges**新しいプッシュを開始**ブログ**データベース</span><span class="sxs-lookup"><span data-stu-id="f4040-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="f4040-136">**SaveChanges**が完了します。</span><span class="sxs-lookup"><span data-stu-id="f4040-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="f4040-137">すべてのクエリ**ブログ**データベースに送信されます</span><span class="sxs-lookup"><span data-stu-id="f4040-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="f4040-138">クエリを返しする結果が書き込まれる**コンソール**</span><span class="sxs-lookup"><span data-stu-id="f4040-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="f4040-139">1 日の見積もりが書き込む**コンソール**</span><span class="sxs-lookup"><span data-stu-id="f4040-139">Quote of the day is written to **Console**</span></span>

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="f4040-141">非同期になります</span><span class="sxs-lookup"><span data-stu-id="f4040-141">Making it asynchronous</span></span>

<span data-ttu-id="f4040-142">このプログラムを起動して実行したら、新しい非同期の使用と、await キーワードを開始できます。</span><span class="sxs-lookup"><span data-stu-id="f4040-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="f4040-143">Program.cs に、次の変更を行いました</span><span class="sxs-lookup"><span data-stu-id="f4040-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="f4040-144">2 行目: を使用して、ステートメント、 **System.Data.Entity**名前空間に対するアクセスを提供、EF の非同期拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="f4040-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="f4040-145">行 4: を使用して、ステートメント、 **System.Threading.Tasks**名前空間では、使用できる、**タスク**型。</span><span class="sxs-lookup"><span data-stu-id="f4040-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="f4040-146">12 & 18: に関しての進行状況を監視するタスクとしてキャプチャされる**PerformSomeDatabaseOperations** (12 行目) し、このプログラムの実行をブロックし、タスク完了 1 回にすべての作業、プログラムが (行 18) に行われます。</span><span class="sxs-lookup"><span data-stu-id="f4040-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="f4040-147">25 行目: 更新プログラムをしました**PerformSomeDatabaseOperations**としてマーク済みである**async**戻って、**タスク**します。</span><span class="sxs-lookup"><span data-stu-id="f4040-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="f4040-148">行 35: 今すぐ SaveChanges の非同期バージョンの呼び出ししていますの完了を待機しています。</span><span class="sxs-lookup"><span data-stu-id="f4040-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="f4040-149">行 42: 今すぐ ToList の非同期バージョンを呼び出すしています、結果を待機しています。</span><span class="sxs-lookup"><span data-stu-id="f4040-149">Line 42: We're now calling hte Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="f4040-150">System.Data.Entity 名前空間の使用可能な拡張メソッドの包括的な一覧、QueryableExtensions クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4040-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="f4040-151">*またを使用する「System.Data.Entity を使用して」を追加する必要がありますステートメント。*</span><span class="sxs-lookup"><span data-stu-id="f4040-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="f4040-152">これで、コードが、非同期、プログラムを実行するとさまざまな実行フローを観察したことができます。</span><span class="sxs-lookup"><span data-stu-id="f4040-152">Now that the code is asyncronous, we can observe a different execution flow when we run the program:</span></span>

1.  <span data-ttu-id="f4040-153">**SaveChanges**新しいプッシュを開始**ブログ**データベースに*より高いの計算に時間が現在のマネージ スレッドで必要ありません、データベースにコマンドが送信されるとします。**PerformDatabaseOperations**メソッドを返します (これは実行が完了していない) 場合でも、Main メソッドで、プログラム フローが続行されます。*</span><span class="sxs-lookup"><span data-stu-id="f4040-153">**SaveChanges** begins to push the new **Blog** to the database *Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2.  <span data-ttu-id="f4040-154">**1 日の見積もりがコンソールに書き込まれる**
    \*待機にマネージ スレッドがブロックされている他の作業は、Main メソッドであるため、データベース操作が完了するまでを呼び出します。これが完了するの残りの部分、 **PerformDatabaseOperations** \*が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f4040-154">**Quote of the day is written to Console**
*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations*** will be executed.</span></span>
3.  <span data-ttu-id="f4040-155">**SaveChanges**が完了します。</span><span class="sxs-lookup"><span data-stu-id="f4040-155">**SaveChanges** completes</span></span>
4.  <span data-ttu-id="f4040-156">すべてのクエリ**ブログ**データベースに送信される*ここでも、マネージ スレッドは無料で、クエリは、データベースの処理中に他の作業を行います。他のすべての実行が完了するため、スレッドはだけ停止待機の呼び出しでただしします。*</span><span class="sxs-lookup"><span data-stu-id="f4040-156">Query for all **Blogs** is sent to the database *Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="f4040-157">クエリを返しする結果が書き込まれる**コンソール**</span><span class="sxs-lookup"><span data-stu-id="f4040-157">Query returns and results are written to **Console**</span></span>

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="f4040-159">したがって、</span><span class="sxs-lookup"><span data-stu-id="f4040-159">The takeaway</span></span>

<span data-ttu-id="f4040-160">ここではさせる簡単な方法を説明しましたの EF の非同期メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f4040-160">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="f4040-161">状況アクティビティの実行時間の長いまたはネットワークにバインドされる可能性がありますそれ以外の場合、アプリケーションをブロックするスレッドの数が多い原因でこれらと同じ戦略を適用できますが非同期の利点は簡単なコンソール アプリで非常に明らかにできない、メモリ使用量を増やします。</span><span class="sxs-lookup"><span data-stu-id="f4040-161">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>

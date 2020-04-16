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
# <a name="async-query-and-save"></a><span data-ttu-id="e0aa5-102">非同期クエリと保存</span><span class="sxs-lookup"><span data-stu-id="e0aa5-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="e0aa5-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e0aa5-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="e0aa5-105">EF6 では、非同期クエリのサポートが導入され、.NET 4.5 で導入された[非同期キーワードと await キーワードを](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)使用して保存されました。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="e0aa5-106">すべてのアプリケーションが非同期の恩恵を受けるわけではありませんが、長時間実行されるネットワークまたは I/O バインドタスクを処理する際に、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="e0aa5-107">非同期を実際に使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="e0aa5-107">When to really use async</span></span>

<span data-ttu-id="e0aa5-108">このチュートリアルの目的は、非同期プログラム実行と同期プログラム実行の違いを簡単に確認できるように非同期の概念を紹介することです。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="e0aa5-109">このチュートリアルでは、非同期プログラミングで利点が得られる主要なシナリオを説明するためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="e0aa5-110">非同期プログラミングは、マネージ スレッドからの計算時間を必要としない操作を待機している間、現在のマネージ スレッド (.NET コードを実行しているスレッド) を解放して他の処理を行うことを主に重点的に行います。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="e0aa5-111">たとえば、データベース エンジンがクエリを処理している間は、.NET コードで何も行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="e0aa5-112">クライアント アプリケーション (WinForms、WPF など) では、非同期操作の実行中に UI の応答性を維持するために現在のスレッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="e0aa5-113">サーバーアプリケーション(ASP.NETなど)では、スレッドを使用して他の着信要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="e0aa5-114">非同期を使用するほとんどのアプリケーションでは、顕著な利点はなく、有害である可能性さえあります。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="e0aa5-115">テスト、プロファイリング、および常識を使用して、特定のシナリオにおける非同期の影響を評価してから、テストにコミットします。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="e0aa5-116">非同期について学ぶリソースを次に示します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="e0aa5-117">ブランドン・ブレイの非同期/待機の概要は.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="e0aa5-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="e0aa5-118">MSDN ライブラリの[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)ページ</span><span class="sxs-lookup"><span data-stu-id="e0aa5-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="e0aa5-119">[非同期を使用してASP.NET Web アプリケーションを構築する方法](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(サーバー スループットの向上のデモを含む)</span><span class="sxs-lookup"><span data-stu-id="e0aa5-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="e0aa5-120">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="e0aa5-120">Create the model</span></span>

<span data-ttu-id="e0aa5-121">[コードファーストワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)を使用してモデルを作成し、データベースを生成しますが、非同期機能は EF Designer で作成されたものを含むすべての EF モデルで動作します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="e0aa5-122">コンソール アプリケーションを作成し、**それを AsyncDemo**と呼びます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="e0aa5-123">エンティティ フレームワーク の NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="e0aa5-124">ソリューション エクスプローラーで **、AsyncDemo**プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="e0aa5-125">**[NuGet パッケージの管理] を選択します。**</span><span class="sxs-lookup"><span data-stu-id="e0aa5-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="e0aa5-126">[NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し **、EntityFramework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="e0aa5-127">[インストール] をクリック**します。**</span><span class="sxs-lookup"><span data-stu-id="e0aa5-127">Click **Install**</span></span>
-   <span data-ttu-id="e0aa5-128">次の実装を使用して**Model.cs**クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="e0aa5-129">同期プログラムの作成</span><span class="sxs-lookup"><span data-stu-id="e0aa5-129">Create a synchronous program</span></span>

<span data-ttu-id="e0aa5-130">EF モデルができたので、それを使用してデータ アクセスを実行するコードを記述しましょう。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="e0aa5-131">**Program.cs**の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="e0aa5-132">このコードは、新しい**ブログ**をデータベースに保存し、データベースからすべての**ブログ**を取得し、**コンソール**に出力する**PerformDatabaseOperations**メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="e0aa5-133">この後、プログラムはコンソールに一日の引用を書き込**みます**。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="e0aa5-134">コードは同期的であるため、プログラムを実行するときに次の実行フローを確認できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="e0aa5-135">**保存変更は**、データベースに新しい**ブログ**をプッシュし始めます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="e0aa5-136">**保存変更が**完了しました</span><span class="sxs-lookup"><span data-stu-id="e0aa5-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="e0aa5-137">すべての**ブログの**クエリがデータベースに送信されます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="e0aa5-138">クエリの戻り値と結果が**コンソール**に書き込まれます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="e0aa5-139">その日の引用はコンソールに書き込**まれます**</span><span class="sxs-lookup"><span data-stu-id="e0aa5-139">Quote of the day is written to **Console**</span></span>

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="e0aa5-141">非同期にする</span><span class="sxs-lookup"><span data-stu-id="e0aa5-141">Making it asynchronous</span></span>

<span data-ttu-id="e0aa5-142">プログラムを起動して実行したので、新しい非同期キーワードと await キーワードの使用を開始できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="e0aa5-143">Program.csに次の変更を加えました</span><span class="sxs-lookup"><span data-stu-id="e0aa5-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="e0aa5-144">2 行目: **System.Data.Entity**名前空間の using ステートメントは、EF 非同期拡張メソッドへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="e0aa5-145">行 4: **System.Threading.Tasks**名前空間の using ステートメントを使用すると、**タスク**の種類を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="e0aa5-146">12 行 & 18: **PerformSomeDatabaseOperations** (12 行目) の進行状況を監視するタスクとしてキャプチャし、プログラムのすべての作業が完了したらこのタスクを実行するプログラムの実行をブロックします (行 18)。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="e0aa5-147">25 行目: 非同期としてマークされ、**タスク**を返すように **、PerformSome データベース操作\*\*\*\*を**更新しました。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="e0aa5-148">35 行目: 非同期バージョンの SaveChanges を呼び出して、完了を待っています。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="e0aa5-149">42 行目: 非同期バージョンの ToList を呼び出して、結果を待っています。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="e0aa5-150">名前空間で使用できる拡張メソッドの包括的な一覧については、クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="e0aa5-151">*また、using ステートメントに "System.Data.Entity を使用する" を追加する必要もあります。*</span><span class="sxs-lookup"><span data-stu-id="e0aa5-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="e0aa5-152">コードが非同期になったので、プログラムを実行するときに別の実行フローを観察できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="e0aa5-153">**保存変更は**、データベースに新しい**ブログ**をプッシュし始めます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="e0aa5-154">*コマンドがデータベースに送信されると、現在のマネージ スレッドでこれ以上の計算時間は必要ありません。メソッド**は**(実行が完了していない場合でも)返し、Main メソッドのプログラム フローが続行されます。*</span><span class="sxs-lookup"><span data-stu-id="e0aa5-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="e0aa5-155">**その日の引用はコンソールに書き込まれます**</span><span class="sxs-lookup"><span data-stu-id="e0aa5-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="e0aa5-156">*Main メソッドで実行する処理がこれ以上存在しないため、データベース操作が完了するまで、待機呼び出しでマネージ スレッドがブロックされます。完了すると、データベース**の操作**の残りの部分が実行されます。*</span><span class="sxs-lookup"><span data-stu-id="e0aa5-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="e0aa5-157">**保存変更が**完了しました</span><span class="sxs-lookup"><span data-stu-id="e0aa5-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="e0aa5-158">すべての**ブログの**クエリがデータベースに送信されます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="e0aa5-159">*マネージ スレッドは、クエリがデータベースで処理されている間は、他の処理を自由に行うことができます。他のすべての実行が完了したので、スレッドは Wait 呼び出しで停止するだけです。*</span><span class="sxs-lookup"><span data-stu-id="e0aa5-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="e0aa5-160">クエリの戻り値と結果が**コンソール**に書き込まれます</span><span class="sxs-lookup"><span data-stu-id="e0aa5-160">Query returns and results are written to **Console**</span></span>  

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="e0aa5-162">テイクアウト</span><span class="sxs-lookup"><span data-stu-id="e0aa5-162">The takeaway</span></span>

<span data-ttu-id="e0aa5-163">EF の非同期メソッドを使いやすい点を見ていった。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="e0aa5-164">非同期の利点は単純なコンソール アプリでは明らかではないかもしれませんが、長時間実行またはネットワーク バインドアクティビティがアプリケーションをブロックしたり、大量のスレッドがメモリの占有領域を増やす場合に、これらの同じ戦略を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e0aa5-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>

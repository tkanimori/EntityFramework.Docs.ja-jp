---
title: 非同期クエリと保存-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: 0642dc13e7aa3906fa1495031c62701fc16f0192
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414861"
---
# <a name="async-query-and-save"></a><span data-ttu-id="73068-102">非同期クエリと保存</span><span class="sxs-lookup"><span data-stu-id="73068-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="73068-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="73068-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="73068-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="73068-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="73068-105">EF6 では、.NET 4.5 で導入された[async キーワードと await キーワード](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)を使用して、非同期クエリと保存のサポートが導入されました。</span><span class="sxs-lookup"><span data-stu-id="73068-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="73068-106">すべてのアプリケーションが非同期性の恩恵を受けるとは限りませんが、長時間実行、ネットワーク、i/o バインドのタスクを処理する際に、クライアントの応答性とサーバーのスケーラビリティを向上させるために使用できます。</span><span class="sxs-lookup"><span data-stu-id="73068-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="73068-107">Async を実際に使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="73068-107">When to really use async</span></span>

<span data-ttu-id="73068-108">このチュートリアルの目的は、非同期の概念を紹介することです。非同期の概念を使用すると、非同期および同期のプログラム実行の違いを簡単に観察できます。</span><span class="sxs-lookup"><span data-stu-id="73068-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="73068-109">このチュートリアルは、非同期プログラミングで利点が得られる主要なシナリオを示すことを目的としていません。</span><span class="sxs-lookup"><span data-stu-id="73068-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="73068-110">非同期プログラミングは、マネージスレッドからのコンピューティング時間を必要としない操作を待機している間に、他の作業を行うために、現在のマネージスレッド (.NET コードを実行しているスレッド) を解放することに主に焦点を合わせています。</span><span class="sxs-lookup"><span data-stu-id="73068-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="73068-111">たとえば、データベースエンジンがクエリを処理している間は、.NET コードによって実行されるものはありません。</span><span class="sxs-lookup"><span data-stu-id="73068-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="73068-112">クライアントアプリケーション (WinForms、WPF など) では、非同期操作の実行中に UI の応答性を維持するために現在のスレッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="73068-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="73068-113">サーバーアプリケーション (ASP.NET など) では、スレッドを使用して他の受信要求を処理できます。これにより、メモリ使用量を削減したり、サーバーのスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="73068-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="73068-114">非同期を使用するほとんどのアプリケーションでは、顕著な利点はなく、悪影響を及ぼす可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="73068-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="73068-115">テスト、プロファイル、および一般的な意味を使用して、特定のシナリオでの非同期の影響を測定してからコミットします。</span><span class="sxs-lookup"><span data-stu-id="73068-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="73068-116">非同期の詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73068-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="73068-117">Brandon Bray の .NET 4.5 の async/await の概要</span><span class="sxs-lookup"><span data-stu-id="73068-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="73068-118">MSDN ライブラリの[非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx)ページ</span><span class="sxs-lookup"><span data-stu-id="73068-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="73068-119">[Async を使用して ASP.NET Web アプリケーションを構築する方法](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(サーバースループットの増加に関するデモを含む)</span><span class="sxs-lookup"><span data-stu-id="73068-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="73068-120">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="73068-120">Create the model</span></span>

<span data-ttu-id="73068-121">ここでは、 [Code First ワークフロー](~/ef6/modeling/code-first/workflows/new-database.md)を使用してモデルを作成し、データベースを生成します。ただし、非同期機能は ef デザイナーで作成されたものを含むすべての ef モデルで動作します。</span><span class="sxs-lookup"><span data-stu-id="73068-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="73068-122">コンソールアプリケーションを作成し、 **Asyncdemo**を呼び出す</span><span class="sxs-lookup"><span data-stu-id="73068-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="73068-123">EntityFramework NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="73068-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="73068-124">ソリューションエクスプローラーで、 **Asyncdemo**プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="73068-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="73068-125">**[NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="73068-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="73068-126">NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="73068-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="73068-127">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="73068-127">Click **Install**</span></span>
-   <span data-ttu-id="73068-128">次の実装を使用して**Model.cs**クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="73068-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="73068-129">同期プログラムを作成する</span><span class="sxs-lookup"><span data-stu-id="73068-129">Create a synchronous program</span></span>

<span data-ttu-id="73068-130">EF モデルが完成したので、いくつかのデータアクセスを実行するために使用するコードを記述してみましょう。</span><span class="sxs-lookup"><span data-stu-id="73068-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="73068-131">**Program.cs**の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="73068-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="73068-132">このコードは、新しい**ブログ**をデータベースに保存し、データベースからすべての**ブログ**を取得して**コンソール**に出力する、実行**databaseoperations**メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="73068-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="73068-133">その後、プログラムはその日の引用符を**コンソール**に書き込みます。</span><span class="sxs-lookup"><span data-stu-id="73068-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="73068-134">コードは同期的であるため、プログラムを実行したときに次の実行フローを観察できます。</span><span class="sxs-lookup"><span data-stu-id="73068-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="73068-135">**SaveChanges**は、データベースへの新しい**ブログ**のプッシュを開始します。</span><span class="sxs-lookup"><span data-stu-id="73068-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="73068-136">**SaveChanges**の完了</span><span class="sxs-lookup"><span data-stu-id="73068-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="73068-137">すべての**ブログ**のクエリがデータベースに送信されます</span><span class="sxs-lookup"><span data-stu-id="73068-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="73068-138">クエリが返され、結果が**コンソール**に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="73068-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="73068-139">日の引用が**コンソール**に書き込まれます</span><span class="sxs-lookup"><span data-stu-id="73068-139">Quote of the day is written to **Console**</span></span>

![同期出力](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="73068-141">非同期にする</span><span class="sxs-lookup"><span data-stu-id="73068-141">Making it asynchronous</span></span>

<span data-ttu-id="73068-142">プログラムを起動して実行したので、新しい async キーワードと await キーワードの使用を開始できます。</span><span class="sxs-lookup"><span data-stu-id="73068-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="73068-143">Program.cs には次の変更が加えられました。</span><span class="sxs-lookup"><span data-stu-id="73068-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="73068-144">行 2: system.string 名前空間の using ステートメントにより、EF async 拡張メソッドにアクセス**できます。**</span><span class="sxs-lookup"><span data-stu-id="73068-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="73068-145">行 4: system.string 名前空間の using ステートメントでは、**タスク**の種類を使用**できます。**</span><span class="sxs-lookup"><span data-stu-id="73068-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="73068-146">12行目 & 18: 実行の進行状況を監視するタスクとしてキャプチャしています (12 行目)。プログラムの**すべての作業**が完了したら、このタスクのプログラム実行をブロックします (18 行目)。</span><span class="sxs-lookup"><span data-stu-id="73068-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="73068-147">行 25:**非同期**としてマークされ、**タスク**が返されるように、**データベース操作**を更新しました。</span><span class="sxs-lookup"><span data-stu-id="73068-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="73068-148">行 35: 現在、SaveChanges の非同期バージョンを呼び出して、完了を待機しています。</span><span class="sxs-lookup"><span data-stu-id="73068-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="73068-149">行 42: 現在、非同期バージョンの ToList を呼び出し、結果を待機しています。</span><span class="sxs-lookup"><span data-stu-id="73068-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="73068-150">System.string 名前空間の使用可能な拡張メソッドの包括的な一覧については、QueryableExtensions クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73068-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="73068-151">*また、using ステートメントに "using system.string" を追加する必要もあります。*</span><span class="sxs-lookup"><span data-stu-id="73068-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="73068-152">コードが非同期になったので、プログラムの実行時に別の実行フローを観察できます。</span><span class="sxs-lookup"><span data-stu-id="73068-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="73068-153">**SaveChanges**は、データベースへの新しい**ブログ**のプッシュを開始します。</span><span class="sxs-lookup"><span data-stu-id="73068-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="73068-154">*コマンドがデータベースに送信されると、現在のマネージスレッドでは、それ以上のコンピューティング時間は必要ありません。実行が完了していなくても、このメソッドはを返します。 Main メソッドのプログラムフローは続行**されます**。*</span><span class="sxs-lookup"><span data-stu-id="73068-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="73068-155">**日の引用がコンソールに書き込まれます**</span><span class="sxs-lookup"><span data-stu-id="73068-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="73068-156">*Main メソッドで実行する作業がなくなったため、データベース操作が完了するまで、待機呼び出しでマネージスレッドがブロックされます。完了すると、残りの実行**データベース操作**が実行されます。*</span><span class="sxs-lookup"><span data-stu-id="73068-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="73068-157">**SaveChanges**の完了</span><span class="sxs-lookup"><span data-stu-id="73068-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="73068-158">すべての**ブログ**のクエリがデータベースに送信されます</span><span class="sxs-lookup"><span data-stu-id="73068-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="73068-159">*この場合も、データベースでクエリが処理されている間は、マネージスレッドで他の作業を自由に行うことができます。他のすべての実行が完了したため、スレッドは待機呼び出しを停止します。*</span><span class="sxs-lookup"><span data-stu-id="73068-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="73068-160">クエリが返され、結果が**コンソール**に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="73068-160">Query returns and results are written to **Console**</span></span>  

![非同期出力](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="73068-162">通じ重要</span><span class="sxs-lookup"><span data-stu-id="73068-162">The takeaway</span></span>

<span data-ttu-id="73068-163">EF の非同期メソッドを簡単に使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="73068-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="73068-164">非同期の利点は、単純なコンソールアプリでは明らかでない場合がありますが、実行時間の長いまたはネットワークにバインドされたアクティビティによってアプリケーションがブロックされる可能性がある場合や、多数のスレッドがメモリフットプリントを増やします。</span><span class="sxs-lookup"><span data-stu-id="73068-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>

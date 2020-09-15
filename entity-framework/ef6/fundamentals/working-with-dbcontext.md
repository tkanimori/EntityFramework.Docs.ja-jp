---
title: DbContext の使用-EF6
description: Entity Framework 6 での DbContext の使用
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 83f3f318c6cd1b0ea440bd472a651ff4454716f1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072149"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="8c3af-103">DbContext の操作</span><span class="sxs-lookup"><span data-stu-id="8c3af-103">Working with DbContext</span></span>

<span data-ttu-id="8c3af-104">Entity Framework を使用して .NET オブジェクトを使用してデータのクエリ、挿入、更新、および削除を行うには、まず、モデルで定義されているエンティティとリレーションシップをデータベース内のテーブルにマップする [モデルを作成](xref:ef6/modeling/index) する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="8c3af-105">モデルを作成すると、アプリケーションが対話するプライマリクラスは `System.Data.Entity.DbContext` (多くの場合、コンテキストクラスと呼ばれます) になります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="8c3af-106">モデルに関連付けられている DbContext を使用して、次のことを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="8c3af-107">クエリの作成と実行</span><span class="sxs-lookup"><span data-stu-id="8c3af-107">Write and execute queries</span></span>   
- <span data-ttu-id="8c3af-108">クエリ結果をエンティティオブジェクトとして具体化する</span><span class="sxs-lookup"><span data-stu-id="8c3af-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="8c3af-109">これらのオブジェクトに加えられた変更を追跡する</span><span class="sxs-lookup"><span data-stu-id="8c3af-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="8c3af-110">オブジェクトの変更をデータベースに保存する</span><span class="sxs-lookup"><span data-stu-id="8c3af-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="8c3af-111">メモリ内のオブジェクトを UI コントロールにバインドする</span><span class="sxs-lookup"><span data-stu-id="8c3af-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="8c3af-112">このページでは、コンテキストクラスを管理する方法に関するガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="8c3af-113">DbContext 派生クラスの定義</span><span class="sxs-lookup"><span data-stu-id="8c3af-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="8c3af-114">コンテキストを使用する場合は、DbContext から派生するクラスを定義し、コンテキスト内の指定されたエンティティのコレクションを表す Dbcontext プロパティを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8c3af-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="8c3af-115">EF デザイナーを使用している場合は、コンテキストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="8c3af-116">Code First を使用する場合は、通常、コンテキストを自分で記述します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="8c3af-117">コンテキストを取得した後は、これらのプロパティを使用して、コンテキスト内のエンティティを照会、追加 ( `Add` またはメソッドを使用 `Attach` ) または削除 (を使用 `Remove` ) できます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="8c3af-118">コンテキストオブジェクトのプロパティへのアクセスは、 `DbSet` 指定された型のすべてのエンティティを返す開始クエリを表します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="8c3af-119">プロパティにアクセスするだけではクエリが実行されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8c3af-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="8c3af-120">クエリは次の場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-120">A query is executed when:</span></span>  

- <span data-ttu-id="8c3af-121">`foreach` (C#) または `For Each` (Visual Basic) ステートメントによって列挙された場合。</span><span class="sxs-lookup"><span data-stu-id="8c3af-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="8c3af-122">これは、、、などのコレクション操作によって列挙され `ToArray` `ToDictionary` `ToList` ます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="8c3af-123">やなどの LINQ `First` 演算子 `Any` は、クエリの最も外側の部分で指定されます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="8c3af-124">次のいずれかのメソッドが呼び出されます。 `Load` `DbEntityEntry.Reload`  `Database.ExecuteSqlCommand` `DbSet<T>.Find` 指定したキーを持つエンティティがコンテキストに既に読み込まれていない場合は、拡張メソッド、、、および。</span><span class="sxs-lookup"><span data-stu-id="8c3af-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="8c3af-125">有効期間</span><span class="sxs-lookup"><span data-stu-id="8c3af-125">Lifetime</span></span>  

<span data-ttu-id="8c3af-126">コンテキストの有効期間は、インスタンスが作成されると開始され、インスタンスが破棄またはガベージコレクトされるときに終了します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="8c3af-127">コンテキストで制御されるすべてのリソースをブロックの最後に破棄する場合は、 **を** 使用します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="8c3af-128">**を**使用すると、コンパイラは自動的に try/finally ブロックを作成し、 **finally**ブロックで dispose を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="8c3af-129">コンテキストの有効期間を決定するときの一般的なガイドラインを次に示します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="8c3af-130">Web アプリケーションを操作する場合は、要求ごとにコンテキストインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="8c3af-131">Windows Presentation Foundation (WPF) または Windows フォームを使用する場合は、フォームごとにコンテキストインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="8c3af-132">これにより、コンテキストによって提供される変更追跡機能を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="8c3af-133">コンテキストインスタンスが依存関係挿入コンテナーによって作成されている場合、通常、コンテキストを破棄するのはコンテナーの役割です。</span><span class="sxs-lookup"><span data-stu-id="8c3af-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="8c3af-134">コンテキストがアプリケーションコードで作成されている場合は、不要になったときにコンテキストを破棄することを忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="8c3af-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="8c3af-135">実行時間の長いコンテキストを使用する場合は、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="8c3af-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="8c3af-136">オブジェクトとその参照をメモリに読み込むと、コンテキストのメモリ消費量が急速に増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="8c3af-137">これは、パフォーマンスを低下させる原因になります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="8c3af-138">コンテキストはスレッドセーフではないため、同時に処理を行う複数のスレッド間で共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="8c3af-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="8c3af-139">例外によってコンテキストが回復不能な状態になると、アプリケーション全体が終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="8c3af-140">データの照会時と更新時の時間差が大きくなると、コンカレンシーに関する問題が生じる可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="8c3af-141">接続</span><span class="sxs-lookup"><span data-stu-id="8c3af-141">Connections</span></span>  

<span data-ttu-id="8c3af-142">既定では、コンテキストはデータベースへの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="8c3af-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="8c3af-143">コンテキストが開き、必要に応じて接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="8c3af-144">たとえば、コンテキストは、クエリを実行するための接続を開き、すべての結果セットが処理されたときに接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="8c3af-145">接続の開閉をより詳細に制御する必要がある場合もあります。</span><span class="sxs-lookup"><span data-stu-id="8c3af-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="8c3af-146">たとえば、SQL Server Compact を使用する場合、パフォーマンスを向上させるために、アプリケーションの有効期間中、データベースに対して開いている接続を個別に維持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8c3af-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="8c3af-147">`Connection` プロパティを使用することにより、手動でこのプロセスを管理できます。</span><span class="sxs-lookup"><span data-stu-id="8c3af-147">You can manage this process manually by using the `Connection` property.</span></span>  

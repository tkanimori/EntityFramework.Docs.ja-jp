---
title: DbContext の使用-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
ms.openlocfilehash: d961ffd8bed7f5b2f82dcfa30fc0241b7437be50
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413883"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="864b4-102">DbContext の操作</span><span class="sxs-lookup"><span data-stu-id="864b4-102">Working with DbContext</span></span>

<span data-ttu-id="864b4-103">Entity Framework を使用して .NET オブジェクトを使用してデータのクエリ、挿入、更新、および削除を行うには、まず、モデルで定義されているエンティティとリレーションシップをデータベース内のテーブルにマップする[モデルを作成](~/ef6/modeling/index.md)する必要があります。</span><span class="sxs-lookup"><span data-stu-id="864b4-103">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](~/ef6/modeling/index.md) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="864b4-104">モデルを作成すると、アプリケーションが対話するプライマリクラスが `System.Data.Entity.DbContext` (多くの場合、コンテキストクラスと呼ばれます) になります。</span><span class="sxs-lookup"><span data-stu-id="864b4-104">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="864b4-105">モデルに関連付けられている DbContext を使用して、次のことを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="864b4-105">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="864b4-106">クエリの作成と実行</span><span class="sxs-lookup"><span data-stu-id="864b4-106">Write and execute queries</span></span>   
- <span data-ttu-id="864b4-107">クエリ結果をエンティティオブジェクトとして具体化する</span><span class="sxs-lookup"><span data-stu-id="864b4-107">Materialize query results as entity objects</span></span>
- <span data-ttu-id="864b4-108">これらのオブジェクトに加えられた変更を追跡する</span><span class="sxs-lookup"><span data-stu-id="864b4-108">Track changes that are made to those objects</span></span>
- <span data-ttu-id="864b4-109">オブジェクトの変更をデータベースに保存する</span><span class="sxs-lookup"><span data-stu-id="864b4-109">Persist object changes back on the database</span></span>
- <span data-ttu-id="864b4-110">メモリ内のオブジェクトを UI コントロールにバインドする</span><span class="sxs-lookup"><span data-stu-id="864b4-110">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="864b4-111">このページでは、コンテキストクラスを管理する方法に関するガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="864b4-111">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="864b4-112">DbContext 派生クラスの定義</span><span class="sxs-lookup"><span data-stu-id="864b4-112">Defining a DbContext derived class</span></span>  

<span data-ttu-id="864b4-113">コンテキストを使用する場合は、DbContext から派生するクラスを定義し、コンテキスト内の指定されたエンティティのコレクションを表す Dbcontext プロパティを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="864b4-113">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="864b4-114">EF デザイナーを使用している場合は、コンテキストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="864b4-114">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="864b4-115">Code First を使用する場合は、通常、コンテキストを自分で記述します。</span><span class="sxs-lookup"><span data-stu-id="864b4-115">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="864b4-116">コンテキストがわかれば、これらのプロパティを使用してクエリを実行したり、(`Add` または `Attach` メソッドを使用して) 追加したり、コンテキストでエンティティを削除 `Remove`したりできます。</span><span class="sxs-lookup"><span data-stu-id="864b4-116">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="864b4-117">コンテキストオブジェクトの `DbSet` プロパティへのアクセスは、指定された型のすべてのエンティティを返す開始クエリを表します。</span><span class="sxs-lookup"><span data-stu-id="864b4-117">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="864b4-118">プロパティにアクセスするだけではクエリが実行されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="864b4-118">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="864b4-119">クエリは次の場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="864b4-119">A query is executed when:</span></span>  

- <span data-ttu-id="864b4-120">`foreach` (C#) または `For Each` (Visual Basic) ステートメントによって列挙された場合。</span><span class="sxs-lookup"><span data-stu-id="864b4-120">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="864b4-121">`ToArray`、`ToDictionary`、`ToList`などのコレクション操作によって列挙されます。</span><span class="sxs-lookup"><span data-stu-id="864b4-121">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="864b4-122">`First` や `Any` などの LINQ 演算子は、クエリの最も外側の部分で指定されます。</span><span class="sxs-lookup"><span data-stu-id="864b4-122">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="864b4-123">次のいずれかのメソッドが呼び出されます。指定したキーを持つエンティティがコンテキストに既に読み込まれていない場合、`Load` 拡張メソッド、`DbEntityEntry.Reload`、`Database.ExecuteSqlCommand`、および `DbSet<T>.Find`です。</span><span class="sxs-lookup"><span data-stu-id="864b4-123">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="864b4-124">有効期間</span><span class="sxs-lookup"><span data-stu-id="864b4-124">Lifetime</span></span>  

<span data-ttu-id="864b4-125">コンテキストの有効期間は、インスタンスが作成されると開始され、インスタンスが破棄またはガベージコレクトされるときに終了します。</span><span class="sxs-lookup"><span data-stu-id="864b4-125">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="864b4-126">コンテキストで制御されるすべてのリソースをブロックの最後に破棄する場合は、**を**使用します。</span><span class="sxs-lookup"><span data-stu-id="864b4-126">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="864b4-127">**を**使用すると、コンパイラは自動的に try/finally ブロックを作成し、 **finally**ブロックで dispose を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="864b4-127">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="864b4-128">コンテキストの有効期間を決定するときの一般的なガイドラインを次に示します。</span><span class="sxs-lookup"><span data-stu-id="864b4-128">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="864b4-129">Web アプリケーションを操作する場合は、要求ごとにコンテキストインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="864b4-129">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="864b4-130">Windows Presentation Foundation (WPF) または Windows フォームを使用する場合は、フォームごとにコンテキストインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="864b4-130">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="864b4-131">これにより、コンテキストによって提供される変更追跡機能を使用できます。</span><span class="sxs-lookup"><span data-stu-id="864b4-131">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="864b4-132">コンテキストインスタンスが依存関係挿入コンテナーによって作成されている場合、通常、コンテキストを破棄するのはコンテナーの役割です。</span><span class="sxs-lookup"><span data-stu-id="864b4-132">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="864b4-133">コンテキストがアプリケーションコードで作成されている場合は、不要になったときにコンテキストを破棄することを忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="864b4-133">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="864b4-134">実行時間の長いコンテキストを使用する場合は、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="864b4-134">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="864b4-135">オブジェクトとその参照をメモリに読み込むと、コンテキストのメモリ消費量が急速に増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="864b4-135">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="864b4-136">これは、パフォーマンスを低下させる原因になります。</span><span class="sxs-lookup"><span data-stu-id="864b4-136">This may cause performance issues.</span></span>  
    - <span data-ttu-id="864b4-137">コンテキストはスレッドセーフではないため、同時に処理を行う複数のスレッド間で共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="864b4-137">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="864b4-138">例外によってコンテキストが回復不能な状態になると、アプリケーション全体が終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="864b4-138">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="864b4-139">データの照会時と更新時の時間差が大きくなると、コンカレンシーに関する問題が生じる可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="864b4-139">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="864b4-140">接続</span><span class="sxs-lookup"><span data-stu-id="864b4-140">Connections</span></span>  

<span data-ttu-id="864b4-141">既定では、コンテキストはデータベースへの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="864b4-141">By default, the context manages connections to the database.</span></span> <span data-ttu-id="864b4-142">コンテキストが開き、必要に応じて接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="864b4-142">The context opens and closes connections as needed.</span></span> <span data-ttu-id="864b4-143">たとえば、コンテキストは、クエリを実行するための接続を開き、すべての結果セットが処理されたときに接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="864b4-143">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="864b4-144">接続の開閉をより詳細に制御する必要がある場合もあります。</span><span class="sxs-lookup"><span data-stu-id="864b4-144">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="864b4-145">たとえば、SQL Server Compact を使用する場合、パフォーマンスを向上させるために、アプリケーションの有効期間中、データベースに対して開いている接続を個別に維持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="864b4-145">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="864b4-146">`Connection` プロパティを使用することにより、手動でこのプロセスを管理できます。</span><span class="sxs-lookup"><span data-stu-id="864b4-146">You can manage this process manually by using the `Connection` property.</span></span>  

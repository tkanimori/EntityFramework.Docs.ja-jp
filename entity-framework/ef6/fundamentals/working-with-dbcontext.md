---
title: DbContext の EF6 の使用
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
caps.latest.revision: 3
ms.openlocfilehash: 865c9883ce25f405a173791df4e46b98550cd41f
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121859"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="1ff05-102">DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="1ff05-102">Working with DbContext</span></span>

<span data-ttu-id="1ff05-103">クエリ、挿入、更新、および .NET オブジェクトを使用してデータを削除する Entity Framework を使用するにはまずに[モデルを作成する](~/ef6/modeling/index.md)エンティティとデータベース内のテーブル モデルで定義されているリレーションシップにマップします。</span><span class="sxs-lookup"><span data-stu-id="1ff05-103">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](~/ef6/modeling/index.md) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="1ff05-104">アプリケーションの対話の主要なクラスは、モデルを作成したら、 `System.Data.Entity.DbContext` (コンテキストのクラスとも呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="1ff05-104">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="1ff05-105">モデルに関連付けられている DbContext を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-105">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="1ff05-106">書き込みし、クエリの実行</span><span class="sxs-lookup"><span data-stu-id="1ff05-106">Write and execute queries</span></span>   
- <span data-ttu-id="1ff05-107">エンティティ オブジェクトとしてクエリ結果を具体化します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-107">Materialize query results as entity objects</span></span>
- <span data-ttu-id="1ff05-108">これらのオブジェクトに加えられた変更を追跡します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-108">Track changes that are made to those objects</span></span>
- <span data-ttu-id="1ff05-109">オブジェクトの変更、データベースをバックアップの保持します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-109">Persist object changes back on the database</span></span>
- <span data-ttu-id="1ff05-110">メモリ内のオブジェクトを UI コントロールにバインド</span><span class="sxs-lookup"><span data-stu-id="1ff05-110">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="1ff05-111">このページには、コンテキスト クラスを管理する方法のいくつかのガイダンスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-111">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="1ff05-112">DbContext 派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-112">Defining a DbContext derived class</span></span>  

<span data-ttu-id="1ff05-113">コンテキストを使用することをお勧めの方法では、DbContext から派生し、コンテキスト内の指定したエンティティのコレクションを表す DbSet プロパティを公開するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-113">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="1ff05-114">EF デザイナーを使用する場合、コンテキストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-114">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="1ff05-115">Code First で作業している場合は通常、コンテキスト自分で作成しました。</span><span class="sxs-lookup"><span data-stu-id="1ff05-115">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="1ff05-116">クエリを実行、追加は、コンテキストを使用すると、(を使用して`Add`または`Attach`メソッド) または削除 (を使用して`Remove`) これらのプロパティをコンテキスト内のエンティティ。</span><span class="sxs-lookup"><span data-stu-id="1ff05-116">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="1ff05-117">アクセス、`DbSet`コンテキスト オブジェクトのプロパティを指定した型のすべてのエンティティを返す開始クエリを表します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-117">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="1ff05-118">だけプロパティへのアクセスは、クエリを実行できませんに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1ff05-118">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="1ff05-119">クエリを実行する場合。</span><span class="sxs-lookup"><span data-stu-id="1ff05-119">A query is executed when:</span></span>  

- <span data-ttu-id="1ff05-120">`foreach` (C#) または `For Each` (Visual Basic) ステートメントによって列挙された場合。</span><span class="sxs-lookup"><span data-stu-id="1ff05-120">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="1ff05-121">など、コレクションの操作で列挙される`ToArray`、 `ToDictionary`、または`ToList`します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-121">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="1ff05-122">などの LINQ 演算子`First`または`Any`クエリの最も外側の部分で指定されます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-122">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="1ff05-123">次のメソッドのいずれかと呼ばれます。`Load`拡張メソッドを`DbEntityEntry.Reload`、 `Database.ExecuteSqlCommand`、および`DbSet<T>.Find`指定のキーを持つエンティティは既にコンテキストに読み込まれている場合は、します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-123">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="1ff05-124">有効期間</span><span class="sxs-lookup"><span data-stu-id="1ff05-124">Lifetime</span></span>  

<span data-ttu-id="1ff05-125">コンテキストの有効期間は、インスタンスが作成され、インスタンスが破棄またはガベージ コレクトされたときに終了を開始します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-125">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="1ff05-126">使用**を使用して**コンテキストが制御ブロックの最後で破棄するすべてのリソースが必要なかどうか。</span><span class="sxs-lookup"><span data-stu-id="1ff05-126">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="1ff05-127">使用すると**を使用して**、コンパイラは try/finally ブロックを自動的に作成およびで dispose を呼び出します、**最後に**ブロックします。</span><span class="sxs-lookup"><span data-stu-id="1ff05-127">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="1ff05-128">次のコンテキストの有効期間を決定する場合に、一般的なガイドラインを示します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-128">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="1ff05-129">Web アプリケーションを使用する場合は、要求ごとにコンテキスト インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-129">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="1ff05-130">Windows Presentation Foundation (WPF) または Windows フォームを使用する場合は、フォームごとコンテキスト インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-130">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="1ff05-131">これにより、変更の追跡機能を使用してそのコンテキストを提供します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-131">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="1ff05-132">コンテキストのインスタンスは、依存関係の注入コンテナーで作成される場合、は、通常、コンテキストを破棄するコンテナーの役割。</span><span class="sxs-lookup"><span data-stu-id="1ff05-132">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="1ff05-133">アプリケーション コードでは、コンテキストを作成する場合は、必要でなくなったとき、コンテキストを破棄する注意してください。</span><span class="sxs-lookup"><span data-stu-id="1ff05-133">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="1ff05-134">実行時間の長いコンテキストを使用する場合は、次を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="1ff05-134">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="1ff05-135">メモリに複数のオブジェクトとそれらの参照を読み込むと、コンテキストのメモリ使用量は急速に増える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ff05-135">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="1ff05-136">これは、パフォーマンスを低下させる原因になります。</span><span class="sxs-lookup"><span data-stu-id="1ff05-136">This may cause performance issues.</span></span>  
    - <span data-ttu-id="1ff05-137">コンテキストがスレッド セーフなしたがってこの属性は複数のスレッドでの作業を同時に実行全体でいない共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ff05-137">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="1ff05-138">例外は、回復不可能な状態であるコンテキストにより、アプリケーション全体は終了できます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-138">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="1ff05-139">データの照会時と更新時の時間差が大きくなると、同時実行に関する問題が生じる可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="1ff05-139">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="1ff05-140">接続</span><span class="sxs-lookup"><span data-stu-id="1ff05-140">Connections</span></span>  

<span data-ttu-id="1ff05-141">既定では、コンテキストは、データベースへの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-141">By default, the context manages connections to the database.</span></span> <span data-ttu-id="1ff05-142">コンテキストが開き、必要に応じて、接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-142">The context opens and closes connections as needed.</span></span> <span data-ttu-id="1ff05-143">たとえば、コンテキストはクエリを実行する接続を開き、すべての結果セットが処理されたときに、接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-143">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="1ff05-144">接続の開閉をより詳細に制御する必要がある場合もあります。</span><span class="sxs-lookup"><span data-stu-id="1ff05-144">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="1ff05-145">たとえば、SQL Server Compact を使用する場合、パフォーマンスを向上させるために、アプリケーションの有効期間にわたってデータベースに別の開いている接続を維持するために多くの場合、推奨します。</span><span class="sxs-lookup"><span data-stu-id="1ff05-145">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="1ff05-146">`Connection` プロパティを使用することにより、手動でこのプロセスを管理できます。</span><span class="sxs-lookup"><span data-stu-id="1ff05-146">You can manage this process manually by using the `Connection` property.</span></span>  

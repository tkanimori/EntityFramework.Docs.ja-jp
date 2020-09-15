---
title: エンティティのクエリおよび検索 - EF6
description: Entity Framework 6 でのエンティティのクエリおよび検索
author: divega
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: d55c88280bcf164457da89ec58c180e9aa1ad12d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072994"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="f64fe-103">エンティティのクエリおよび検索</span><span class="sxs-lookup"><span data-stu-id="f64fe-103">Querying and Finding Entities</span></span>
<span data-ttu-id="f64fe-104">このトピックでは、Entity Framework を使用した、LINQ および Find メソッドを含むデータのさまざまなクエリ方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f64fe-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="f64fe-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="f64fe-106">クエリを使用したエンティティの検索</span><span class="sxs-lookup"><span data-stu-id="f64fe-106">Finding entities using a query</span></span>  

<span data-ttu-id="f64fe-107">DbSet および IDbSet には、IQueryable が実装されているので、データベースに対して LINQ クエリを記述する開始点として使用できます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="f64fe-108">ここは LINQ に関して詳細に説明する場所ではありませんが、簡単な例を次にいくつか挙げます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

<span data-ttu-id="f64fe-109">なお、DbSet および IDbSet は、常にデータベースに対してクエリが作成し、返されたエンティティがコンテキストに既に含まれる場合でも、常にデータベースに対してラウンドトリップを行います。</span><span class="sxs-lookup"><span data-stu-id="f64fe-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="f64fe-110">クエリは次のときにデータベースに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="f64fe-111">**foreach** (C#) または **For Each** (Visual Basic) ステートメントによって列挙された場合。</span><span class="sxs-lookup"><span data-stu-id="f64fe-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="f64fe-112">[ToArray](https://msdn.microsoft.com/library/bb298736)、[ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary)、または [ToList](https://msdn.microsoft.com/library/bb342261) などのコレクション操作によって列挙された場合。</span><span class="sxs-lookup"><span data-stu-id="f64fe-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="f64fe-113">[First](https://msdn.microsoft.com/library/bb291976) や [Any](https://msdn.microsoft.com/library/bb337697) などの LINQ 演算子がクエリの最も外側で指定された場合。</span><span class="sxs-lookup"><span data-stu-id="f64fe-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="f64fe-114">DbSet の [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) 拡張メソッド、[DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx)、および Database.ExecuteSqlCommand メソッドが呼び出された場合。</span><span class="sxs-lookup"><span data-stu-id="f64fe-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="f64fe-115">データベースから結果が返されると、コンテキストに存在しないオブジェクトはコンテキストにアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="f64fe-116">オブジェクトが既にコンテキストに存在する場合、既存のオブジェクトが返されます (エントリに格納されているオブジェクトのプロパティの現在の値と元の値が、データ ソースの値で上書きされることは**ありません**)。</span><span class="sxs-lookup"><span data-stu-id="f64fe-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="f64fe-117">データベースにはまだ保存されていないコンテキストに追加されたエンティティは、クエリの実行では結果セットの一部として返されません。</span><span class="sxs-lookup"><span data-stu-id="f64fe-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="f64fe-118">コンテキスト内のデータを取得するには、「[ローカル データ](xref:ef6/querying/local-data)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f64fe-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="f64fe-119">クエリによってデータベースから行が返されない場合、結果は **null** ではなく空のコレクションになります。</span><span class="sxs-lookup"><span data-stu-id="f64fe-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="f64fe-120">主キーを使用したエンティティの検索</span><span class="sxs-lookup"><span data-stu-id="f64fe-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="f64fe-121">DbSet の Find メソッドは、主キーの値を使用してコンテキストで追跡されているエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="f64fe-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="f64fe-122">コンテキストにエンティティがない場合、クエリはデータベースに送られ、そこからエンティティが検索されます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="f64fe-123">コンテキストまたはデータベース内にそのエンティティが見つからない場合は null が返されます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="f64fe-124">Find は、次の 2 つの重要な点でクエリを使用する場合とは異なります。</span><span class="sxs-lookup"><span data-stu-id="f64fe-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="f64fe-125">データベースに対するラウンドトリップは、特定のキーを持つエンティティがコンテキストにない場合のみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="f64fe-126">Find は、追加された状態のエンティティを返します。</span><span class="sxs-lookup"><span data-stu-id="f64fe-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="f64fe-127">つまり、Find はコンテキストに追加されているけれども、データベースにまだ保存されていないエンティティを返します。</span><span class="sxs-lookup"><span data-stu-id="f64fe-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="f64fe-128">主キーを使用したエンティティの検索</span><span class="sxs-lookup"><span data-stu-id="f64fe-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="f64fe-129">次のコードでは、Find の用途をいくつか示しています。</span><span class="sxs-lookup"><span data-stu-id="f64fe-129">The following code shows some uses of Find:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="f64fe-130">複合主キーを使用したエンティティの検索</span><span class="sxs-lookup"><span data-stu-id="f64fe-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="f64fe-131">Entity Framework では、エンティティに複合キー (2 つ以上のプロパティで構成されているキー) が許可されています。</span><span class="sxs-lookup"><span data-stu-id="f64fe-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="f64fe-132">たとえば、特定のブログのユーザー設定を表す BlogSettings エンティティがあるとします。</span><span class="sxs-lookup"><span data-stu-id="f64fe-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="f64fe-133">ユーザーは、1 つのブログで BlogSettings を 1 つしか持つことができないので、BlogSettings の主キーを BlogId と Username の組み合わせにすることができます。</span><span class="sxs-lookup"><span data-stu-id="f64fe-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="f64fe-134">次のコードでは、BlogId = 3 と Username = "johndoe1987" の BlogSettings の検索を試行します。</span><span class="sxs-lookup"><span data-stu-id="f64fe-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="f64fe-135">なお、複合キーがある場合、ColumnAttribute または fluent API を使用して、複合キーのプロパティの順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f64fe-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="f64fe-136">Find に対する呼び出しでは、そのキーを形成する値を指定するときに、この順序を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f64fe-136">The call to Find must use this order when specifying the values that form the key.</span></span>  

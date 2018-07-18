---
title: 生 SQL クエリ - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
caps.latest.revision: 3
ms.openlocfilehash: 1d968604cfa500784c4699b0923512572a06d773
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122299"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="adbc9-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="adbc9-102">Raw SQL Queries</span></span>
<span data-ttu-id="adbc9-103">Entity Framework エンティティ クラスで LINQ を使用してクエリを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="adbc9-104">ただし、データベースに対して直接 SQL そのものを使用してクエリを実行する時間があります。</span><span class="sxs-lookup"><span data-stu-id="adbc9-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="adbc9-105">これは、Code First モデルの現在のストアド プロシージャへのマッピングをサポートしていないことができますが、ストアド プロシージャの呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="adbc9-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="adbc9-107">エンティティの SQL クエリの記述</span><span class="sxs-lookup"><span data-stu-id="adbc9-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="adbc9-108">DbSet に SqlQuery メソッドは、エンティティのインスタンスを返す書き込まれる生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="adbc9-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="adbc9-109">れる LINQ クエリが返されたかどうかと同様、返されたオブジェクトがコンテキストによって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="adbc9-110">例えば:</span><span class="sxs-lookup"><span data-stu-id="adbc9-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="adbc9-111">LINQ クエリと同様、クエリが実行されない結果が列挙されるまでに注意してください: ToList への呼び出しで上記の例で実行されます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="adbc9-112">生 SQL クエリが 2 つの理由に書き込まれるたびに、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="adbc9-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="adbc9-113">最初に、実際には、要求された型のエンティティのみが返されることを確認するクエリを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="adbc9-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="adbc9-114">たとえば、継承などの機能を使用する場合が正しくない CLR 型のエンティティを作成するクエリを記述する簡単です。</span><span class="sxs-lookup"><span data-stu-id="adbc9-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="adbc9-115">次に、生の SQL クエリの種類によっては、SQL インジェクション攻撃の特にの潜在的なセキュリティ リスクを公開します。</span><span class="sxs-lookup"><span data-stu-id="adbc9-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="adbc9-116">このような攻撃から保護するための適切な方法で、クエリでパラメーターを使用することを確認します。</span><span class="sxs-lookup"><span data-stu-id="adbc9-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="adbc9-117">ストアド プロシージャからのエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="adbc9-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="adbc9-118">DbSet.SqlQuery を使用して、ストアド プロシージャの結果からエンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="adbc9-119">たとえば、次のコードは、dbo を呼び出します。データベースの GetBlogs プロシージャ:</span><span class="sxs-lookup"><span data-stu-id="adbc9-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="adbc9-120">次の構文を使用してストアド プロシージャにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="adbc9-121">エンティティ型以外の SQL クエリの作成</span><span class="sxs-lookup"><span data-stu-id="adbc9-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="adbc9-122">データベース クラスの SqlQuery メソッドを使用して、プリミティブ型を含む、任意の型のインスタンスを返す SQL クエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="adbc9-123">例えば:</span><span class="sxs-lookup"><span data-stu-id="adbc9-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="adbc9-124">オブジェクトのエンティティ型のインスタンスである場合でも、データベースで SqlQuery から返される結果は、コンテキストによって追跡ことはありません。</span><span class="sxs-lookup"><span data-stu-id="adbc9-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="adbc9-125">生のコマンドをデータベースに送信します。</span><span class="sxs-lookup"><span data-stu-id="adbc9-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="adbc9-126">非クエリ コマンドは、データベースで ExecuteSqlCommand メソッドを使用してデータベースに送信できます。</span><span class="sxs-lookup"><span data-stu-id="adbc9-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="adbc9-127">例えば:</span><span class="sxs-lookup"><span data-stu-id="adbc9-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="adbc9-128">エンティティが読み込まれたまたは、データベースから再読み込みされるまで ExecuteSqlCommand を使用して、データベース内のデータに加えられた変更は、コンテキストに不透明なことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="adbc9-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="adbc9-129">出力パラメーター</span><span class="sxs-lookup"><span data-stu-id="adbc9-129">Output Parameters</span></span>  

<span data-ttu-id="adbc9-130">出力パラメーターを使用している場合は、結果が完全に読み取られるまでに、その値は使用できません。</span><span class="sxs-lookup"><span data-stu-id="adbc9-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="adbc9-131">これは、DbDataReader の基になる動作によるものを参照してください[DataReader によるデータの取得](http://go.microsoft.com/fwlink/?LinkID=398589)の詳細。</span><span class="sxs-lookup"><span data-stu-id="adbc9-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  

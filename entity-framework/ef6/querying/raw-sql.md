---
title: 生の SQL クエリ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 168aee67186535bf2a50705e86bfc5a88147e369
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414471"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="72fda-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="72fda-102">Raw SQL Queries</span></span>
<span data-ttu-id="72fda-103">Entity Framework を使用すると、エンティティクラスで LINQ を使用してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="72fda-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="72fda-104">ただし、生の SQL を使用してデータベースに対して直接クエリを実行することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="72fda-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="72fda-105">これにはストアドプロシージャの呼び出しが含まれます。これは、ストアドプロシージャへのマッピングを現在サポートしていない Code First モデルに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="72fda-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="72fda-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="72fda-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="72fda-107">エンティティに対する SQL クエリの記述</span><span class="sxs-lookup"><span data-stu-id="72fda-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="72fda-108">DbSet の SqlQuery メソッドを使用すると、エンティティインスタンスを返す生の SQL クエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="72fda-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="72fda-109">返されたオブジェクトは、LINQ クエリによって返された場合と同様に、コンテキストによって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="72fda-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="72fda-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="72fda-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="72fda-111">LINQ クエリの場合と同様に、クエリは結果が列挙されるまで実行されないことに注意してください。上記の例では、これは ToList の呼び出しを使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="72fda-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="72fda-112">生の SQL クエリが2つの理由で書き込まれるたびに、注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="72fda-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="72fda-113">最初に、要求された型のエンティティのみが返されるようにクエリを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="72fda-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="72fda-114">たとえば、継承などの機能を使用する場合、誤った CLR 型のエンティティを作成するクエリを記述するのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="72fda-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="72fda-115">次に、一部の種類の未加工の SQL クエリは、特に SQL インジェクション攻撃に関連する潜在的なセキュリティリスクを露呈します。</span><span class="sxs-lookup"><span data-stu-id="72fda-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="72fda-116">このような攻撃から保護するために、クエリでパラメーターを正しい方法で使用していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="72fda-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="72fda-117">ストアドプロシージャからのエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="72fda-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="72fda-118">DbSet SqlQuery を使用して、ストアドプロシージャの結果からエンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="72fda-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="72fda-119">たとえば、次のコードは dbo を呼び出します。データベース内の GetBlogs プロシージャ:</span><span class="sxs-lookup"><span data-stu-id="72fda-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="72fda-120">次の構文を使用して、ストアドプロシージャにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="72fda-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="72fda-121">非エンティティ型に対する SQL クエリの記述</span><span class="sxs-lookup"><span data-stu-id="72fda-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="72fda-122">プリミティブ型を含む任意の型のインスタンスを返す SQL クエリは、データベースクラスの SqlQuery メソッドを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="72fda-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="72fda-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="72fda-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="72fda-124">オブジェクトがエンティティ型のインスタンスであっても、データベースに対して SqlQuery から返された結果はコンテキストによって追跡されません。</span><span class="sxs-lookup"><span data-stu-id="72fda-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="72fda-125">生のコマンドをデータベースに送信する</span><span class="sxs-lookup"><span data-stu-id="72fda-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="72fda-126">クエリ以外のコマンドは、データベースで ExecuteSqlCommand メソッドを使用してデータベースに送信できます。</span><span class="sxs-lookup"><span data-stu-id="72fda-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="72fda-127">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="72fda-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="72fda-128">ExecuteSqlCommand を使用してデータベース内のデータに加えられた変更は、エンティティがデータベースから読み込まれるか、データベースから再読み込みされるまでコンテキストに対して不透明になります。</span><span class="sxs-lookup"><span data-stu-id="72fda-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="72fda-129">出力パラメーター</span><span class="sxs-lookup"><span data-stu-id="72fda-129">Output Parameters</span></span>  

<span data-ttu-id="72fda-130">出力パラメーターが使用されている場合、結果が完全に読み取られるまで、値は使用できません。</span><span class="sxs-lookup"><span data-stu-id="72fda-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="72fda-131">これは、DbDataReader の基になる動作に起因します。詳細については、「 [DataReader を使用したデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72fda-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  

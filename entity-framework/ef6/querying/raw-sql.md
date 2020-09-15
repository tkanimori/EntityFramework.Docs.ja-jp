---
title: 生の SQL クエリ-EF6
description: Entity Framework 6 の生の SQL クエリ
author: divega
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: 30fab3029d71fb560493af279231e46828aa43d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073887"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="9398e-103">生の SQL クエリ (EF6)</span><span class="sxs-lookup"><span data-stu-id="9398e-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="9398e-104">Entity Framework を使用すると、エンティティクラスで LINQ を使用してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="9398e-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="9398e-105">ただし、生の SQL を使用してデータベースに対して直接クエリを実行することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9398e-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="9398e-106">これにはストアドプロシージャの呼び出しが含まれます。これは、ストアドプロシージャへのマッピングを現在サポートしていない Code First モデルに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9398e-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="9398e-107">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="9398e-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="9398e-108">エンティティに対する SQL クエリの記述</span><span class="sxs-lookup"><span data-stu-id="9398e-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="9398e-109">DbSet の SqlQuery メソッドを使用すると、エンティティインスタンスを返す生の SQL クエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="9398e-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="9398e-110">返されたオブジェクトは、LINQ クエリによって返された場合と同様に、コンテキストによって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="9398e-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="9398e-111">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9398e-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="9398e-112">LINQ クエリの場合と同様に、クエリは結果が列挙されるまで実行されないことに注意してください。上記の例では、これは ToList の呼び出しを使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="9398e-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="9398e-113">生の SQL クエリが2つの理由で書き込まれるたびに、注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="9398e-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="9398e-114">最初に、要求された型のエンティティのみが返されるようにクエリを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9398e-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="9398e-115">たとえば、継承などの機能を使用する場合、誤った CLR 型のエンティティを作成するクエリを記述するのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="9398e-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="9398e-116">次に、一部の種類の未加工の SQL クエリは、特に SQL インジェクション攻撃に関連する潜在的なセキュリティリスクを露呈します。</span><span class="sxs-lookup"><span data-stu-id="9398e-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="9398e-117">このような攻撃から保護するために、クエリでパラメーターを正しい方法で使用していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9398e-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="9398e-118">ストアドプロシージャからのエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="9398e-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="9398e-119">DbSet SqlQuery を使用して、ストアドプロシージャの結果からエンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="9398e-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="9398e-120">たとえば、次のコードは dbo を呼び出します。データベース内の GetBlogs プロシージャ:</span><span class="sxs-lookup"><span data-stu-id="9398e-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="9398e-121">次の構文を使用して、ストアドプロシージャにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="9398e-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="9398e-122">非エンティティ型に対する SQL クエリの記述</span><span class="sxs-lookup"><span data-stu-id="9398e-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="9398e-123">プリミティブ型を含む任意の型のインスタンスを返す SQL クエリは、データベースクラスの SqlQuery メソッドを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="9398e-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="9398e-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9398e-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="9398e-125">オブジェクトがエンティティ型のインスタンスであっても、データベースに対して SqlQuery から返された結果はコンテキストによって追跡されません。</span><span class="sxs-lookup"><span data-stu-id="9398e-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="9398e-126">生のコマンドをデータベースに送信する</span><span class="sxs-lookup"><span data-stu-id="9398e-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="9398e-127">クエリ以外のコマンドは、データベースで ExecuteSqlCommand メソッドを使用してデータベースに送信できます。</span><span class="sxs-lookup"><span data-stu-id="9398e-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="9398e-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9398e-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="9398e-129">ExecuteSqlCommand を使用してデータベース内のデータに加えられた変更は、エンティティがデータベースから読み込まれるか、データベースから再読み込みされるまでコンテキストに対して不透明になります。</span><span class="sxs-lookup"><span data-stu-id="9398e-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="9398e-130">出力パラメーター</span><span class="sxs-lookup"><span data-stu-id="9398e-130">Output Parameters</span></span>  

<span data-ttu-id="9398e-131">出力パラメーターが使用されている場合、結果が完全に読み取られるまで、値は使用できません。</span><span class="sxs-lookup"><span data-stu-id="9398e-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="9398e-132">これは、DbDataReader の基になる動作に起因します。詳細については、「 [DataReader を使用したデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9398e-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  

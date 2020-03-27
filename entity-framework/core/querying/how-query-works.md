---
title: クエリのしくみ - EF Core
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e8a50efe31468ea8df211602636dd474550bc0ef
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136241"
---
# <a name="how-queries-work"></a><span data-ttu-id="1bdf9-102">クエリのしくみ</span><span class="sxs-lookup"><span data-stu-id="1bdf9-102">How Queries Work</span></span>

<span data-ttu-id="1bdf9-103">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="1bdf9-104">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="1bdf9-105">クエリの全体像</span><span class="sxs-lookup"><span data-stu-id="1bdf9-105">The life of a query</span></span>

<span data-ttu-id="1bdf9-106">各クエリが通過するプロセスの高次元での概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="1bdf9-107">LINQ クエリは、データベース プロバイダーで処理するために用意された表現をビルドするために、Entity Framework Core によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="1bdf9-108">クエリが実行されるたびにこの処理が実行される必要がないように、結果はキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="1bdf9-109">結果が、データベース プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="1bdf9-110">データベース プロバイダーは、クエリのどの部分がデータベースで評価できるかを識別します。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="1bdf9-111">クエリのこれらの部分は、データベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="1bdf9-112">クエリがデータべースに送信され、結果セットが返されます (結果は、エンティティ インスタンスではなく、データベースからの値です)。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-112">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="1bdf9-113">結果セット内の各項目で、</span><span class="sxs-lookup"><span data-stu-id="1bdf9-113">For each item in the result set</span></span>
   1. <span data-ttu-id="1bdf9-114">これが追跡クエリの場合、EF では、データがコンテキスト インスタンスの変更追跡に既にあるエンティティを表しているかどうかをチェックします。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="1bdf9-115">既にある場合、既存のエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="1bdf9-116">ない場合、新しいエンティティが作成され、変更追跡が設定されて、新しいエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="1bdf9-117">追跡できないクエリの場合は、常に新しいエンティティが作成されて返されます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-117">If this is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="1bdf9-118">クエリの実行時</span><span class="sxs-lookup"><span data-stu-id="1bdf9-118">When queries are executed</span></span>

<span data-ttu-id="1bdf9-119">LINQ 演算子を呼び出すと、単純にクエリのメモリ内表現がビルドされます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-119">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="1bdf9-120">クエリは、結果が使用されるときにデータベースに送信されるだけです。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-120">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="1bdf9-121">クエリでの結果がデータベースに送信される最も一般的な操作は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-121">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="1bdf9-122">`for` ループ内で結果を反復処理する</span><span class="sxs-lookup"><span data-stu-id="1bdf9-122">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="1bdf9-123">`ToList`、`ToArray`、`Single`、`Count` または同等の非同期オーバーロードなどの演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="1bdf9-123">Using an operator such as `ToList`, `ToArray`, `Single`, `Count` or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="1bdf9-124">**ユーザー入力を常に検証する:** クエリでパラメーターを使用し、リテラルをエスケープすることで、EF Core によって SQL インジェクション攻撃から保護されていますが、入力は検証されません。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-124">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="1bdf9-125">信頼されていないソースの値が LINQ クエリで使用されたり、エンティティ プロパティに割り当てられたり、他の EF Core API に渡される前に、アプリケーションの要件ごとに適切な検証を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-125">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="1bdf9-126">これには、動的にクエリを構築する際に使用されるユーザー入力も含まれます。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-126">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="1bdf9-127">LINQ を使用している場合であっても、式をビルドするためのユーザー入力を許可しているなら、目的の式だけは構築できることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bdf9-127">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>

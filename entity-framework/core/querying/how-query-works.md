---
title: クエリのしくみ - EF Core
author: rowanmiller
ms.date: 09/26/2018
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: ba0d68469530e6272ffbb51946d7856122a261c7
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656238"
---
# <a name="how-queries-work"></a><span data-ttu-id="91584-102">クエリのしくみ</span><span class="sxs-lookup"><span data-stu-id="91584-102">How Queries Work</span></span>

<span data-ttu-id="91584-103">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="91584-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="91584-104">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="91584-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="91584-105">クエリの全体像</span><span class="sxs-lookup"><span data-stu-id="91584-105">The life of a query</span></span>

<span data-ttu-id="91584-106">各クエリが通過するプロセスの高次元での概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="91584-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="91584-107">LINQ クエリは、データベース プロバイダーで処理するために用意された表現をビルドするために、Entity Framework Core によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="91584-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="91584-108">クエリが実行されるたびにこの処理が実行される必要がないように、結果はキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="91584-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="91584-109">結果が、データベース プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="91584-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="91584-110">データベース プロバイダーは、クエリのどの部分がデータベースで評価できるかを識別します。</span><span class="sxs-lookup"><span data-stu-id="91584-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="91584-111">クエリのこれらの部分は、データベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="91584-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="91584-112">1 つまたは複数のクエリがデータべースに送信され、結果セットが返されます (結果は、エンティティ インスタンスではなく、データベースからの値です)。</span><span class="sxs-lookup"><span data-stu-id="91584-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="91584-113">結果セット内の各項目で、</span><span class="sxs-lookup"><span data-stu-id="91584-113">For each item in the result set</span></span>
   1. <span data-ttu-id="91584-114">これが追跡クエリの場合、EF では、データがコンテキスト インスタンスの変更追跡に既にあるエンティティを表しているかどうかをチェックします。</span><span class="sxs-lookup"><span data-stu-id="91584-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="91584-115">既にある場合、既存のエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="91584-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="91584-116">ない場合、新しいエンティティが作成され、変更追跡が設定されて、新しいエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="91584-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="91584-117">これが追跡なしのクエリの場合、EF では、データがこのクエリの結果セットに既にあるエンティティを表しているかどうかをチェックします。</span><span class="sxs-lookup"><span data-stu-id="91584-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="91584-118">既にある場合、既存のエンティティが返されます<sup>(1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="91584-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="91584-119">ない場合、新しいエンティティが作成されて、返されます。</span><span class="sxs-lookup"><span data-stu-id="91584-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="91584-120"><sup>(1)</sup> 既に返されたエンティティの追跡を継続するために、追跡なしクエリでは弱参照が使用されます。</span><span class="sxs-lookup"><span data-stu-id="91584-120"><sup>(1)</sup> No-tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="91584-121">同じ ID の前の結果が範囲外になっている場合は、ガベージ コレクションが実行され、新しいエンティティ インスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="91584-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="91584-122">クエリの実行時</span><span class="sxs-lookup"><span data-stu-id="91584-122">When queries are executed</span></span>

<span data-ttu-id="91584-123">LINQ 演算子を呼び出すと、単純にクエリのメモリ内表現がビルドされます。</span><span class="sxs-lookup"><span data-stu-id="91584-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="91584-124">クエリは、結果が使用されるときにデータベースに送信されるだけです。</span><span class="sxs-lookup"><span data-stu-id="91584-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="91584-125">クエリでの結果がデータベースに送信される最も一般的な操作は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="91584-125">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="91584-126">`for` ループ内で結果を反復処理する</span><span class="sxs-lookup"><span data-stu-id="91584-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="91584-127">`ToList`、`ToArray`、`Single`、`Count` などの演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="91584-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="91584-128">クエリの結果を UI にデータバインドする</span><span class="sxs-lookup"><span data-stu-id="91584-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="91584-129">**ユーザー入力を常に検証する:** クエリでパラメーターを使用し、リテラルをエスケープすることで、EF Core によって SQL インジェクション攻撃から保護されていますが、入力は検証されません。</span><span class="sxs-lookup"><span data-stu-id="91584-129">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="91584-130">信頼されていないソースの値が LINQ クエリで使用されたり、エンティティ プロパティに割り当てられたり、他の EF Core API に渡される前に、アプリケーションの要件ごとに適切な検証を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91584-130">Appropriate validation, per the application's requirements, should be performed before values from untrusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="91584-131">これには、動的にクエリを構築する際に使用されるユーザー入力も含まれます。</span><span class="sxs-lookup"><span data-stu-id="91584-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="91584-132">LINQ を使用している場合であっても、式をビルドするためのユーザー入力を許可しているなら、目的の式だけは構築できることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91584-132">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>

---
title: "どのクエリ動作の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
ms.technology: entity-framework-core
uid: core/querying/overview
ms.openlocfilehash: 7fd2940d559f82016d7a8fc3fdcf3af0d5b8bc8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="how-queries-work"></a><span data-ttu-id="dde78-102">クエリのしくみ</span><span class="sxs-lookup"><span data-stu-id="dde78-102">How Queries Work</span></span>

<span data-ttu-id="dde78-103">Entity Framework Core では、データベースからデータをクエリする言語統合クエリ (LINQ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="dde78-103">Entity Framework Core uses Language Integrate Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="dde78-104">LINQ では、派生コンテキストおよびエンティティ クラスに基づいて厳密に型指定のクエリを作成する (C#) (または任意の .NET 言語) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="dde78-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="dde78-105">クエリの有効期間</span><span class="sxs-lookup"><span data-stu-id="dde78-105">The life of a query</span></span>

<span data-ttu-id="dde78-106">各クエリを通過するプロセスの高レベルな概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="dde78-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="dde78-107">データベース プロバイダーで処理する準備が整っている表現の構築に Entity Framework のコアで LINQ クエリの処理します。</span><span class="sxs-lookup"><span data-stu-id="dde78-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="dde78-108">結果をキャッシュすると、この処理は、クエリが実行されるたびに実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dde78-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="dde78-109">結果は、データベース プロバイダーに渡されます</span><span class="sxs-lookup"><span data-stu-id="dde78-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="dde78-110">データベース プロバイダーを識別、クエリのどの部分は、データベースで評価されます。</span><span class="sxs-lookup"><span data-stu-id="dde78-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="dde78-111">クエリのこの部分は、データベースの特定のクエリ言語 (リレーショナル データベースの SQL の場合など) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="dde78-111">These parts of the query are translated to database specific query language (e.g. SQL for a relational database)</span></span>
   3. <span data-ttu-id="dde78-112">1 つまたは複数のクエリは、データベースと、返される結果セットに送信されます (結果は、エンティティのインスタンスではない、データベースから値を)</span><span class="sxs-lookup"><span data-stu-id="dde78-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="dde78-113">結果セット内の各アイテムの</span><span class="sxs-lookup"><span data-stu-id="dde78-113">For each item in the result set</span></span>
   1. <span data-ttu-id="dde78-114">追跡クエリの場合は、EF はデータ コンテキスト インスタンスに対して変更の追跡ツールで既にエンティティを表すかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="dde78-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="dde78-115">既存のエンティティが返された場合は、</span><span class="sxs-lookup"><span data-stu-id="dde78-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="dde78-116">ない場合は、新しいエンティティを作成、変更の追跡をセットアップ、および新しいエンティティが返されます</span><span class="sxs-lookup"><span data-stu-id="dde78-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="dde78-117">EF では、データがこのクエリの結果セット内の既存のエンティティを表すかどうかはチェックなしの追跡クエリの場合は、</span><span class="sxs-lookup"><span data-stu-id="dde78-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="dde78-118">そのため、既存のエンティティが返される場合は<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="dde78-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="dde78-119">ない場合、新しいエンティティが作成され、返される</span><span class="sxs-lookup"><span data-stu-id="dde78-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="dde78-120"><sup>(1)</sup>追跡クエリを使用していない弱い参照返されたエンティティを追跡します。</span><span class="sxs-lookup"><span data-stu-id="dde78-120"><sup>(1)</sup> No tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="dde78-121">同じ id を持つ前の結果がスコープ外に出るし、ガベージ コレクションの実行する場合、は、エンティティの新しいインスタンスを取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dde78-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="dde78-122">クエリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="dde78-122">When queries are executed</span></span>

<span data-ttu-id="dde78-123">LINQ 演算子を呼び出すときにクエリのメモリ内表現を構築するだけです。</span><span class="sxs-lookup"><span data-stu-id="dde78-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="dde78-124">クエリは結果を消費するときに、データベースにのみ送信されます。</span><span class="sxs-lookup"><span data-stu-id="dde78-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="dde78-125">データベースに送信されるクエリになる最も一般的な演算は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dde78-125">The most common operations that result in the query being sent to the database are:</span></span>
* <span data-ttu-id="dde78-126">結果を反復処理する、`for`ループ</span><span class="sxs-lookup"><span data-stu-id="dde78-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="dde78-127">などの演算子を使用して`ToList`、 `ToArray`、 `Single`、`Count`</span><span class="sxs-lookup"><span data-stu-id="dde78-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="dde78-128">データ バインドを UI にクエリの結果</span><span class="sxs-lookup"><span data-stu-id="dde78-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="dde78-129">**ユーザー入力を必ず検証:**中の EF は SQL インジェクション攻撃から保護を提供、入力のすべての一般的な検証を実行しません。</span><span class="sxs-lookup"><span data-stu-id="dde78-129">**Always validate user input:** While EF does provide protection from SQL injection attacks, it does not do any general validation of input.</span></span> <span data-ttu-id="dde78-130">したがって場合など、エンティティ プロパティに割り当てられている LINQ クエリで使用される Api に渡される値には、信頼できないソースし、アプリケーション要件に合わせて、適切な検証を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dde78-130">Therefore if values being passed to APIs, used in LINQ queries, assigned to entity properties, etc., come from an untrusted source then appropriate validation, per your application requirements, should be performed.</span></span> <span data-ttu-id="dde78-131">これには、動的にクエリを構築するために使用されるユーザー入力が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dde78-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="dde78-132">LINQ を使用して、目的の式のみよりも確認する必要があります。 式を構築を構築できる、ユーザー入力を受け付けている場合場合でも。</span><span class="sxs-lookup"><span data-stu-id="dde78-132">Even when using LINQ, if you are accepting user input to build expressions you need to make sure than only intended expressions can be constructed.</span></span>

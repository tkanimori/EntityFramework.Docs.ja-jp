---
title: クエリのしくみ - EF Core
description: Entity Framework Core 内部でのクエリのコンパイルおよび実行の方法に関する一般的な情報
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: f3a6794b9bfdf70ae40bc8e97ee41861931b9b46
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071187"
---
# <a name="how-queries-work"></a><span data-ttu-id="0757f-103">クエリのしくみ</span><span class="sxs-lookup"><span data-stu-id="0757f-103">How Queries Work</span></span>

<span data-ttu-id="0757f-104">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="0757f-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="0757f-105">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="0757f-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="0757f-106">クエリの全体像</span><span class="sxs-lookup"><span data-stu-id="0757f-106">The life of a query</span></span>

<span data-ttu-id="0757f-107">次の説明は、各クエリが通過するプロセスの大まかな概要です。</span><span class="sxs-lookup"><span data-stu-id="0757f-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="0757f-108">LINQ クエリは、データベース プロバイダーで処理するために用意された表現をビルドするために、Entity Framework Core によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="0757f-109">クエリが実行されるたびにこの処理が実行される必要がないように、結果はキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="0757f-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="0757f-110">結果が、データベース プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="0757f-111">データベース プロバイダーは、クエリのどの部分がデータベースで評価できるかを識別します。</span><span class="sxs-lookup"><span data-stu-id="0757f-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="0757f-112">クエリのこれらの部分は、データベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="0757f-113">クエリがデータべースに送信され、結果セットが返されます (結果は、エンティティ インスタンスではなく、データベースからの値です)。</span><span class="sxs-lookup"><span data-stu-id="0757f-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="0757f-114">結果セット内の各項目で、</span><span class="sxs-lookup"><span data-stu-id="0757f-114">For each item in the result set</span></span>
   1. <span data-ttu-id="0757f-115">クエリが追跡クエリの場合は、EF では、データがコンテキスト インスタンスの変更トラッカーで既に存在するエンティティを表しているかどうかをチェックします。</span><span class="sxs-lookup"><span data-stu-id="0757f-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="0757f-116">既にある場合、既存のエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="0757f-117">存在しない場合は、新しいエンティティが作成され、変更追跡が設定されて、新しいエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="0757f-118">クエリが非追跡クエリの場合は、常に新しいエンティティが作成され、返されます。</span><span class="sxs-lookup"><span data-stu-id="0757f-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="0757f-119">クエリの実行時</span><span class="sxs-lookup"><span data-stu-id="0757f-119">When queries are executed</span></span>

<span data-ttu-id="0757f-120">LINQ 演算子を呼び出すと、クエリのメモリ内表現が単純にビルドされます。</span><span class="sxs-lookup"><span data-stu-id="0757f-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="0757f-121">クエリは、結果が使用されるときにデータベースに送信されるだけです。</span><span class="sxs-lookup"><span data-stu-id="0757f-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="0757f-122">クエリでの結果がデータベースに送信される最も一般的な操作は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0757f-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="0757f-123">`for` ループ内で結果を反復処理する</span><span class="sxs-lookup"><span data-stu-id="0757f-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="0757f-124">`ToList`、`ToArray`、`Single`、`Count` または同等の非同期オーバーロードなどの演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="0757f-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="0757f-125">**ユーザー入力を常に検証する:** クエリでパラメーターを使用し、リテラルをエスケープすることで、EF Core によって SQL インジェクション攻撃から保護されていますが、入力は検証されません。</span><span class="sxs-lookup"><span data-stu-id="0757f-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="0757f-126">信頼されていないソースの値が LINQ クエリで使用されたり、エンティティ プロパティに割り当てられたり、他の EF Core API に渡される前に、アプリケーションの要件ごとに適切な検証を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0757f-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="0757f-127">これには、動的にクエリを構築する際に使用されるユーザー入力も含まれます。</span><span class="sxs-lookup"><span data-stu-id="0757f-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="0757f-128">LINQ を使用している場合であっても、式をビルドするためのユーザー入力を許可しているなら、目的の式だけは構築できることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0757f-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>

---
title: "同時実行の競合の EF コアの処理"
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="5e87e-102">同時実行の競合の処理</span><span class="sxs-lookup"><span data-stu-id="5e87e-102">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="5e87e-103">このページは、EF Core での同時実行制御のしくみと、アプリケーションで同時実行の競合を処理する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-103">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="5e87e-104">参照してください[同時実行トークン](xref:core/modeling/concurrency)モデルの同時実行トークンを構成する方法の詳細。</span><span class="sxs-lookup"><span data-stu-id="5e87e-104">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="5e87e-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="5e87e-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="5e87e-106">_データベースの同時実行_状況で複数のプロセスまたはユーザーへのアクセスまたはデータベース内の同じデータを同時に変更を指します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-106">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="5e87e-107">_同時実行制御_同時変更の有無でデータの一貫性を確保するために使用する特定のメカニズムを指します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-107">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="5e87e-108">EF Core を実装する_オプティミスティック同時実行制御_ロックやことにより、複数のプロセスまたはユーザーの同期のオーバーヘッドなしとは独立して変更を加えることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-108">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="5e87e-109">理想的な状況でこれらの変更は互いに影響しませんので、できるとを成功させるのにします。</span><span class="sxs-lookup"><span data-stu-id="5e87e-109">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="5e87e-110">最悪のシナリオで 2 つ以上のプロセスは、競合する変更を行うし、うち片方だけが成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5e87e-110">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="5e87e-111">EF Core での同時実行制御のしくみ</span><span class="sxs-lookup"><span data-stu-id="5e87e-111">How concurrency control works in EF Core</span></span>

<span data-ttu-id="5e87e-112">同時実行トークンを使用してオプティミスティック同時実行制御を実装するように構成のプロパティ: 中に update または delete 操作が実行されるたびに`SaveChanges`元のに対して、データベースに対する同時実行トークンの値が比較EF コアによって読み取られた値。</span><span class="sxs-lookup"><span data-stu-id="5e87e-112">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="5e87e-113">値が一致した場合、操作が完了することができます。</span><span class="sxs-lookup"><span data-stu-id="5e87e-113">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="5e87e-114">値が一致しない場合、EF Core では、他のユーザーが競合する操作が実行して、現在のトランザクションを中止前提としています。</span><span class="sxs-lookup"><span data-stu-id="5e87e-114">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="5e87e-115">別のユーザーが現在の操作と競合する操作を実行すると、状況と呼ばれる_同時実行の競合_です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-115">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="5e87e-116">データベース プロバイダーは、同時実行トークンの値の比較の実装を担当します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-116">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="5e87e-117">リレーショナル データベースの EF コアにはで同時実行トークンの値については、チェックが含まれています、`WHERE`いずれかの句`UPDATE`または`DELETE`ステートメントです。</span><span class="sxs-lookup"><span data-stu-id="5e87e-117">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="5e87e-118">ステートメントを実行した後は、EF コアは、影響を受けた行の数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5e87e-118">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="5e87e-119">行が影響はないと、同時実行の競合が検出されると、および EF コアをスロー`DbUpdateConcurrencyException`です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-119">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="5e87e-120">構成することがありますたとえば、`LastName`で`Person`同時実行トークンであります。</span><span class="sxs-lookup"><span data-stu-id="5e87e-120">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="5e87e-121">ユーザーに任意の更新操作が 同時実行制御チェックが含まれます、`WHERE`句。</span><span class="sxs-lookup"><span data-stu-id="5e87e-121">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="5e87e-122">同時実行の競合を解決します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-122">Resolving concurrency conflicts</span></span>

<span data-ttu-id="5e87e-123">引き続き使用する前の例では、1 人のユーザーが、いくつかの変更を保存しようとしています。、 `Person`、別のユーザーが既に変更されて、 `LastName` 、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5e87e-123">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName` the an exception will be thrown.</span></span>

<span data-ttu-id="5e87e-124">この時点では、アプリケーションでしただけで更新が競合する変更のため失敗したことをユーザーに通知し、上に移動します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-124">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="5e87e-125">このレコードがまだ同じの実際のユーザーを表すことを確認し、操作を再試行するユーザー入力を求めることが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="5e87e-125">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="5e87e-126">このプロセスの例は、_同時実行の競合を解決する_です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-126">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="5e87e-127">同時実行の競合を解決するには、現在の保留中の変更をマージが含まれます`DbContext`値で、データベースにします。</span><span class="sxs-lookup"><span data-stu-id="5e87e-127">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="5e87e-128">どのような値を取得マージして、アプリケーションによって異なります、ユーザー入力から指示可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5e87e-128">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="5e87e-129">**同時実行の競合を解決するために使用可能な値の 3 つのセットがあります。**</span><span class="sxs-lookup"><span data-stu-id="5e87e-129">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

* <span data-ttu-id="5e87e-130">**現在の値**値、アプリケーションがデータベースへの書き込みを試みたを示します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-130">**Current values** are the values that the application was attempting to write to the database.</span></span>

* <span data-ttu-id="5e87e-131">**元の値**はすべての編集が行われる前に、データベースから取得された元の値。</span><span class="sxs-lookup"><span data-stu-id="5e87e-131">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>

* <span data-ttu-id="5e87e-132">**値をデータベース**はデータベースに格納されている値です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-132">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="5e87e-133">同時実行の競合を処理する一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-133">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="5e87e-134">キャッチ`DbUpdateConcurrencyException`中に`SaveChanges`です。</span><span class="sxs-lookup"><span data-stu-id="5e87e-134">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="5e87e-135">使用して`DbUpdateConcurrencyException.Entries`影響を受けるエンティティの一連の変更を準備します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-135">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="5e87e-136">データベースの現在の値を反映するように、同時実行トークンの元の値を更新します。</span><span class="sxs-lookup"><span data-stu-id="5e87e-136">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="5e87e-137">競合が発生しないまでは、プロセスを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="5e87e-137">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="5e87e-138">次の例では、`Person.FirstName`と`Person.LastName`は、同時実行トークンとしてセットアップします。</span><span class="sxs-lookup"><span data-stu-id="5e87e-138">In the following example, `Person.FirstName` and `Person.LastName` are setup as concurrency tokens.</span></span> <span data-ttu-id="5e87e-139">`// TODO:`を保存する値を選択するアプリケーション固有のロジックを含める場所にコメントです。</span><span class="sxs-lookup"><span data-stu-id="5e87e-139">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]

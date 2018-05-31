---
title: 同時実行の競合の処理 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
ms.locfileid: "29745487"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="6af4e-102">同時実行の競合の処理</span><span class="sxs-lookup"><span data-stu-id="6af4e-102">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="6af4e-103">このページは、EF Core での同時実行のしくみと、アプリケーションで同時実行の競合を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-103">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="6af4e-104">モデルで同時実行トークンを構成する方法の詳細については、[同時実行トークン](xref:core/modeling/concurrency)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6af4e-104">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="6af4e-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="6af4e-106">"_データベースの同時実行_" とは、複数のプロセスまたはユーザーが、データベース内の同じデータに対して同時にアクセスするか、または変更を加える状況のことです。</span><span class="sxs-lookup"><span data-stu-id="6af4e-106">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="6af4e-107">"_同時実行の制御_" とは、同時実行の変更においてデータの一貫性を保証するために使用される特定のメカニズムのことです。</span><span class="sxs-lookup"><span data-stu-id="6af4e-107">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="6af4e-108">EF Core は、"_オプティミスティック同時実行制御_" を実装しています。これは、同期やロックのオーバーヘッドを伴わずに、複数のプロセスまたはユーザーが独立して変更を加えられることを意味します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-108">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="6af4e-109">理想的な状況であれば、これらの変更が互いに干渉しないため、正常に処理できます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-109">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="6af4e-110">最悪のケースのシナリオでは、2 つ以上のプロセスが、競合する変更を行おうとして、どちらか一方しか正常に処理されません。</span><span class="sxs-lookup"><span data-stu-id="6af4e-110">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="6af4e-111">EF Core での同時実行制御のしくみ</span><span class="sxs-lookup"><span data-stu-id="6af4e-111">How concurrency control works in EF Core</span></span>

<span data-ttu-id="6af4e-112">同時実行トークンとして構成されるプロパティは、オプティミスティック同時実行制御の実装に使用されます。更新または削除処理が `SaveChanges` の間に実行された場合は常に、データベース上の同時実行トークンの値が、EF Core で読み取られた元の値と比較されます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-112">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="6af4e-113">値が一致した場合、操作は完了できます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-113">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="6af4e-114">値が一致しない場合、EF Core では、別のユーザーが競合する処理を実行したと仮定して、現在のトランザクションを中断します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-114">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="6af4e-115">現在の処理と競合する処理を別のユーザーが実行した場合の状況を、"_同時実行の競合_" といいます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-115">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="6af4e-116">データベース プロバイダーは、同時実行トークン値の比較の実装を担います。</span><span class="sxs-lookup"><span data-stu-id="6af4e-116">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="6af4e-117">リレーショナル データベースでは、EF Core は `UPDATE` や `DELETE` ステートメントの `WHERE` 句に、同時実行トークン値のチェックを含みます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-117">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="6af4e-118">ステートメントを実行した後、EF Core は、影響を受けた行数を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="6af4e-118">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="6af4e-119">影響を受けた行がない場合、同時実行の競合が検出され、EF Core は `DbUpdateConcurrencyException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="6af4e-119">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="6af4e-120">たとえば、`Person` 上で同時実行トークンになるように、`LastName` を構成できます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-120">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="6af4e-121">これにより、Person 上での更新処理に、`WHERE` 句での同時実行のチェックが含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="6af4e-121">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="6af4e-122">同時実行の競合の解決</span><span class="sxs-lookup"><span data-stu-id="6af4e-122">Resolving concurrency conflicts</span></span>

<span data-ttu-id="6af4e-123">前の例に続けて、あるユーザーが `Person` に対するいくつかの変更を保存しようとしたところ、別のユーザーが既に `LastName` を変更していた場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6af4e-123">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName` the an exception will be thrown.</span></span>

<span data-ttu-id="6af4e-124">この時点で、アプリケーションは単純に、変更の競合が原因で更新が成功しなかったことをユーザーに通知して、次の処理へ移ります。</span><span class="sxs-lookup"><span data-stu-id="6af4e-124">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="6af4e-125">しかし、このレコードが引き続き実際の同一人物を表しており、処理を再試行できることを確認するよう、ユーザーに求めることが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="6af4e-125">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="6af4e-126">このプロセスは、"_同時実行の競合の解決_" の例です。</span><span class="sxs-lookup"><span data-stu-id="6af4e-126">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="6af4e-127">同時実行の競合を解決するには、現在の `DbContext` からの保留中の変更をデータベースの値とマージします。</span><span class="sxs-lookup"><span data-stu-id="6af4e-127">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="6af4e-128">どの値がマージされるかは、アプリケーションに基づいて変わります。また、ユーザー入力によって指示することが可能です。</span><span class="sxs-lookup"><span data-stu-id="6af4e-128">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="6af4e-129">**同時実行の競合を解決するために使用可能な 3 つの設定値を次に示します。**</span><span class="sxs-lookup"><span data-stu-id="6af4e-129">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

* <span data-ttu-id="6af4e-130">**現在の値**は、アプリケーションがデータベースへの書き込みを試行している値です。</span><span class="sxs-lookup"><span data-stu-id="6af4e-130">**Current values** are the values that the application was attempting to write to the database.</span></span>

* <span data-ttu-id="6af4e-131">**元の値**は、何らかの編集が行われる前に、データベースから取得された元の値です。</span><span class="sxs-lookup"><span data-stu-id="6af4e-131">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>

* <span data-ttu-id="6af4e-132">**データベース値**は、データベースに現在格納されている値です。</span><span class="sxs-lookup"><span data-stu-id="6af4e-132">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="6af4e-133">同時実行の競合を処理する一般的な方法は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6af4e-133">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="6af4e-134">`SaveChanges` の間に `DbUpdateConcurrencyException` をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="6af4e-134">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="6af4e-135">`DbUpdateConcurrencyException.Entries` を使用して、影響を受けるエンティティに対する新しい変更の設定を準備します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-135">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="6af4e-136">同時実行トークンの元の値を更新して、データベースの現在の値を反映します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-136">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="6af4e-137">競合が発生しなくなるまで、プロセスを再試行します。</span><span class="sxs-lookup"><span data-stu-id="6af4e-137">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="6af4e-138">次の例では、`Person.FirstName` と `Person.LastName` は、同時実行トークンとして設定されています。</span><span class="sxs-lookup"><span data-stu-id="6af4e-138">In the following example, `Person.FirstName` and `Person.LastName` are setup as concurrency tokens.</span></span> <span data-ttu-id="6af4e-139">保存する値を選択するためのアプリケーション固有のロジックを含める場所に、`// TODO:` コメントがあります。</span><span class="sxs-lookup"><span data-stu-id="6af4e-139">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]

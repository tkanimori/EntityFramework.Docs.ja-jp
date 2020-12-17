---
title: 効率的な更新-EF Core
description: Entity Framework Core を使用した効率的な更新のパフォーマンスガイド
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657731"
---
# <a name="efficient-updating"></a><span data-ttu-id="3e5cc-103">効率的な更新</span><span class="sxs-lookup"><span data-stu-id="3e5cc-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="3e5cc-104">バッチ処理</span><span class="sxs-lookup"><span data-stu-id="3e5cc-104">Batching</span></span>

<span data-ttu-id="3e5cc-105">EF Core は、すべての更新を1回のラウンドトリップで自動的にバッチ処理することで、ラウンドトリップを最小限に抑えるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="3e5cc-106">以下、具体例に沿って説明します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="3e5cc-107">上の例では、データベースからブログを読み込み、その名前を変更して、2つの新しいブログを追加しています。これを適用するには、2つの SQL INSERT ステートメントと1つの UPDATE ステートメントをデータベースに送信します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-107">The above loads a blog from the database, changes its name, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="3e5cc-108">ブログのインスタンスが追加されると、これらの変更が1つずつ送信されるのではなく、EF Core によって内部的に追跡され、が呼び出されると、1回のラウンドトリップで実行 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> されます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="3e5cc-109">EF が1回のラウンドトリップでバッチを処理するステートメントの数は、使用されているデータベースプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="3e5cc-110">たとえば、パフォーマンス分析では、4つ未満のステートメントが関係している場合に、SQL Server の効率が低下するようにバッチ処理が行われています。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="3e5cc-111">同様に、バッチ処理の利点は SQL Server に対して40ステートメントの後で低下するので、EF Core は既定では1つのバッチで最大42ステートメントのみを実行し、別のラウンドトリップで追加のステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="3e5cc-112">ユーザーは、これらのしきい値を調整してパフォーマンスを向上させることができますが、これらを変更する前に慎重にベンチマークすることもできます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="3e5cc-113">一括更新</span><span class="sxs-lookup"><span data-stu-id="3e5cc-113">Bulk updates</span></span>

<span data-ttu-id="3e5cc-114">すべての従業員に対して発生させたいと考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="3e5cc-115">この EF Core の一般的な実装は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="3e5cc-116">これは完全に有効なコードですが、パフォーマンスの観点から何が実行されているかを分析してみましょう。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="3e5cc-117">すべての関連する従業員を読み込むために、データベースのラウンドトリップが実行されます。これにより、給与のみが必要な場合でも、従業員のすべての行データがクライアントに取り込まれることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="3e5cc-118">EF Core の変更の追跡では、エンティティの読み込み時にスナップショットを作成し、それらのスナップショットをインスタンスと比較して、どのプロパティが変更されたかを確認します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="3e5cc-119">すべての変更を保存するために、2つ目のデータベースのラウンドトリップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="3e5cc-120">バッチ処理によってすべての変更が1回のラウンドトリップで実行されますが、EF Core は、データベースによって実行される必要がある、従業員ごとに UPDATE ステートメントを送信します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="3e5cc-121">リレーショナルデータベースでは *一括更新* もサポートされているため、上記のは次の1つの SQL ステートメントとして書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="3e5cc-122">これにより、すべての操作が1回のラウンドトリップで実行されます。データベースに実際のデータが読み込まれたり送信されたりすることはなく、EF の変更追跡メカニズムを使用しなくても、オーバーヘッドが増加します。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="3e5cc-123">残念ながら、現在、EF では一括更新を実行するための Api は提供されていません。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="3e5cc-124">これらが導入されるまで、生の SQL を使用して、パフォーマンスが重要な操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="3e5cc-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]

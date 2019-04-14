---
title: テーブル分割 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 4a0bfaf017106a0bfdff084b1c472bdc17459a89
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562588"
---
# <a name="table-splitting"></a><span data-ttu-id="2e083-102">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="2e083-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="2e083-103">この機能は、EF Core 2.0 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="2e083-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="2e083-104">EF Core は、2 つ以上のエンティティを 1 つの行にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="2e083-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="2e083-105">これは呼び出されます_テーブル分割_または_テーブルの共有_します。</span><span class="sxs-lookup"><span data-stu-id="2e083-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="2e083-106">構成</span><span class="sxs-lookup"><span data-stu-id="2e083-106">Configuration</span></span>

<span data-ttu-id="2e083-107">テーブル分割を同じテーブルにマップする必要があるエンティティ型を使用するには、同じ列にマップされている主キーと 1 つのエンティティ型の主キーと同じテーブル内の別の間で構成されている少なくとも 1 つのリレーションシップが。</span><span class="sxs-lookup"><span data-stu-id="2e083-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="2e083-108">テーブル分割の一般的なシナリオが使用して、列のサブセットのみテーブルのパフォーマンス以上、カプセル化。</span><span class="sxs-lookup"><span data-stu-id="2e083-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="2e083-109">この例では`Order`のサブセットを表す`DetailedOrder`します。</span><span class="sxs-lookup"><span data-stu-id="2e083-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="2e083-110">呼び出すだけでなく、必要な構成`HasBaseType((string)null)`マッピングを回避するために`DetailedOrder`と同じ階層に`Order`します。</span><span class="sxs-lookup"><span data-stu-id="2e083-110">In addition to the required configuration we call `HasBaseType((string)null)` to avoid mapping `DetailedOrder` in the same hierarchy as `Order`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

<span data-ttu-id="2e083-111">参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)詳細なコンテキスト。</span><span class="sxs-lookup"><span data-stu-id="2e083-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="2e083-112">使用法</span><span class="sxs-lookup"><span data-stu-id="2e083-112">Usage</span></span>

<span data-ttu-id="2e083-113">保存して、テーブル分割を使用してエンティティのクエリを実行するその他のエンティティと同じ方法で行われます、唯一の違いは、挿入行の共有のすべてのエンティティを追跡する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e083-113">Saving and querying entities using table splitting is done in the same way as other entities, the only difference is that all entities sharing a row must be tracked for the insert.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="2e083-114">同時実行トークン</span><span class="sxs-lookup"><span data-stu-id="2e083-114">Concurrency tokens</span></span>

<span data-ttu-id="2e083-115">同時実行トークンを持つテーブルを共有するエンティティ型のいずれかの場合は、同じテーブルにマップされているエンティティの 1 つだけが更新されたときに古い同時実行トークンの値を回避するために他のすべてのエンティティ型に含まする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e083-115">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="2e083-116">使用側コードに公開を避けるためには、シャドウ状態の 1 つ作成します。</span><span class="sxs-lookup"><span data-stu-id="2e083-116">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
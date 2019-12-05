---
title: テーブル分割-EF Core
description: Entity Framework Core を使用してテーブル分割を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
uid: core/modeling/table-splitting
ms.openlocfilehash: 0e48c516de43cdc2b54c56f1a96f5e01f9fbbbc4
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824558"
---
# <a name="table-splitting"></a><span data-ttu-id="a88a8-103">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="a88a8-103">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="a88a8-104">この機能は EF Core 2.0 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="a88a8-104">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="a88a8-105">EF Core を使用すると、複数のエンティティを1つの行にマップできます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-105">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="a88a8-106">これは、_テーブル分割_または_テーブル共有_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-106">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="a88a8-107">の構成</span><span class="sxs-lookup"><span data-stu-id="a88a8-107">Configuration</span></span>

<span data-ttu-id="a88a8-108">テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="a88a8-108">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="a88a8-109">テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-109">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="a88a8-110">この例では `Order` `DetailedOrder`のサブセットを表します。</span><span class="sxs-lookup"><span data-stu-id="a88a8-110">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="a88a8-111">必要な構成に加えて、`Property(o => o.Status).HasColumnName("Status")` を呼び出して、`DetailedOrder.Status` を `Order.Status`と同じ列にマップします。</span><span class="sxs-lookup"><span data-stu-id="a88a8-111">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> <span data-ttu-id="a88a8-112">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a88a8-112">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="a88a8-113">使用状況</span><span class="sxs-lookup"><span data-stu-id="a88a8-113">Usage</span></span>

<span data-ttu-id="a88a8-114">テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-114">Saving and querying entities using table splitting is done in the same way as other entities.</span></span> <span data-ttu-id="a88a8-115">EF Core 3.0 以降では、依存エンティティ参照を `null`できます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-115">And starting with EF Core 3.0 the dependent entity reference can be `null`.</span></span> <span data-ttu-id="a88a8-116">依存エンティティによって使用されているすべての列がデータベースで `NULL` 場合は、クエリの実行時にインスタンスが作成されません。</span><span class="sxs-lookup"><span data-stu-id="a88a8-116">If all of the columns used by the dependent entity are `NULL` is the database then no instance for it will be created when queried.</span></span> <span data-ttu-id="a88a8-117">また、すべてのプロパティは省略可能であり、`null`に設定されますが、これは想定されていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a88a8-117">This would also happen all of the properties are optional and set to `null`, which might not be expected.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="a88a8-118">同時実行トークン</span><span class="sxs-lookup"><span data-stu-id="a88a8-118">Concurrency tokens</span></span>

<span data-ttu-id="a88a8-119">テーブルを共有するいずれかのエンティティ型が同時実行トークンを持っている場合は、同じテーブルにマップされたエンティティの1つだけが更新されたときに、古い同時実行トークンの値を回避するために、他のすべてのエンティティ型に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="a88a8-119">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="a88a8-120">コンシューマー側のコードにそれを公開しないようにするには、シャドウ状態で1つ作成することができます。</span><span class="sxs-lookup"><span data-stu-id="a88a8-120">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]

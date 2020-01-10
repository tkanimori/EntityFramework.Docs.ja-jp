---
title: テーブル分割-EF Core
description: Entity Framework Core を使用してテーブル分割を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: c38d3ee0efa82f84a1051017ae40c9f3fdd57f1f
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781171"
---
# <a name="table-splitting"></a><span data-ttu-id="50596-103">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="50596-103">Table Splitting</span></span>

<span data-ttu-id="50596-104">EF Core を使用すると、複数のエンティティを1つの行にマップできます。</span><span class="sxs-lookup"><span data-stu-id="50596-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="50596-105">これは、_テーブル分割_または_テーブル共有_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="50596-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="50596-106">の構成</span><span class="sxs-lookup"><span data-stu-id="50596-106">Configuration</span></span>

<span data-ttu-id="50596-107">テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="50596-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="50596-108">テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="50596-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="50596-109">この例では `Order` `DetailedOrder`のサブセットを表します。</span><span class="sxs-lookup"><span data-stu-id="50596-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="50596-110">必要な構成に加えて、`Property(o => o.Status).HasColumnName("Status")` を呼び出して、`DetailedOrder.Status` を `Order.Status`と同じ列にマップします。</span><span class="sxs-lookup"><span data-stu-id="50596-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="50596-111">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="50596-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="50596-112">使用状況</span><span class="sxs-lookup"><span data-stu-id="50596-112">Usage</span></span>

<span data-ttu-id="50596-113">テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。</span><span class="sxs-lookup"><span data-stu-id="50596-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="50596-114">省略可能な依存エンティティ</span><span class="sxs-lookup"><span data-stu-id="50596-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="50596-115">この機能は EF Core 3.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="50596-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="50596-116">依存エンティティによって使用されているすべての列がデータベースで `NULL` されている場合は、クエリの実行時にインスタンスが作成されません。</span><span class="sxs-lookup"><span data-stu-id="50596-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="50596-117">これにより、オプションの依存エンティティをモデル化できます。この場合、プリンシパルのリレーションシッププロパティは null になります。</span><span class="sxs-lookup"><span data-stu-id="50596-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="50596-118">これは、依存しているすべてのプロパティは省略可能であり、`null`に設定されていますが、これは想定されていない可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="50596-118">Note that This would also happen all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="50596-119">同時実行トークン</span><span class="sxs-lookup"><span data-stu-id="50596-119">Concurrency tokens</span></span>

<span data-ttu-id="50596-120">テーブルを共有するいずれかのエンティティ型に同時実行トークンが含まれている場合は、他のすべてのエンティティ型にも含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="50596-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="50596-121">これは、同じテーブルにマップされたエンティティの1つだけが更新される場合に、古い同時実行トークンの値を回避するために必要です。</span><span class="sxs-lookup"><span data-stu-id="50596-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="50596-122">コンシューマー側のコードに同時実行トークンが公開されないようにするには、 [shadow プロパティ](xref:core/modeling/shadow-properties)として作成することができます。</span><span class="sxs-lookup"><span data-stu-id="50596-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]

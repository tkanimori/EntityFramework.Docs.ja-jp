---
title: テーブル分割-EF Core
description: Entity Framework Core を使用してテーブル分割を構成する方法
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 71d332ec3f500f48e12863c71ac44ce4d60699f0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063037"
---
# <a name="table-splitting"></a><span data-ttu-id="e01bb-103">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="e01bb-103">Table Splitting</span></span>

<span data-ttu-id="e01bb-104">EF Core を使用すると、複数のエンティティを1つの行にマップできます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="e01bb-105">これは、 _テーブル分割_ または _テーブル共有_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="e01bb-106">構成</span><span class="sxs-lookup"><span data-stu-id="e01bb-106">Configuration</span></span>

<span data-ttu-id="e01bb-107">テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="e01bb-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="e01bb-108">テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="e01bb-109">この例で `Order` は、のサブセットを表し `DetailedOrder` ます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="e01bb-110">必要な構成に加え `Property(o => o.Status).HasColumnName("Status")` て、を呼び出して、 `DetailedOrder.Status` と同じ列にマップし `Order.Status` ます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="e01bb-111">詳細なコンテキストについては、 [完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e01bb-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="e01bb-112">使用方法</span><span class="sxs-lookup"><span data-stu-id="e01bb-112">Usage</span></span>

<span data-ttu-id="e01bb-113">テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="e01bb-114">省略可能な依存エンティティ</span><span class="sxs-lookup"><span data-stu-id="e01bb-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="e01bb-115">この機能は EF Core 3.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e01bb-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="e01bb-116">依存エンティティによって使用されているすべての列が `NULL` データベース内に存在する場合、クエリの実行時にそのインスタンスのインスタンスは作成されません。</span><span class="sxs-lookup"><span data-stu-id="e01bb-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="e01bb-117">これにより、オプションの依存エンティティをモデル化できます。この場合、プリンシパルのリレーションシッププロパティは null になります。</span><span class="sxs-lookup"><span data-stu-id="e01bb-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="e01bb-118">これは、依存するすべてのプロパティが省略可能で、がに設定されている場合にも発生しますが、これは `null` 想定されていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e01bb-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="e01bb-119">コンカレンシー トークン</span><span class="sxs-lookup"><span data-stu-id="e01bb-119">Concurrency tokens</span></span>

<span data-ttu-id="e01bb-120">テーブルを共有するいずれかのエンティティ型に同時実行トークンが含まれている場合は、他のすべてのエンティティ型にも含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="e01bb-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="e01bb-121">これは、同じテーブルにマップされたエンティティの1つだけが更新される場合に、古い同時実行トークンの値を回避するために必要です。</span><span class="sxs-lookup"><span data-stu-id="e01bb-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="e01bb-122">コンシューマー側のコードに同時実行トークンが公開されないようにするには、 [shadow プロパティ](xref:core/modeling/shadow-properties)として作成することができます。</span><span class="sxs-lookup"><span data-stu-id="e01bb-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]

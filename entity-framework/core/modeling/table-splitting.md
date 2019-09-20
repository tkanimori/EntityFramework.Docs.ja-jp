---
title: テーブル分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 684fcfbb66debfd1b89e23c8aaf0a32909378c6b
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149186"
---
# <a name="table-splitting"></a><span data-ttu-id="6ebb5-102">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="6ebb5-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="6ebb5-103">この機能は EF Core 2.0 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="6ebb5-104">EF Core を使用すると、複数のエンティティを1つの行にマップできます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="6ebb5-105">これは、_テーブル分割_または_テーブル共有_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="6ebb5-106">構成</span><span class="sxs-lookup"><span data-stu-id="6ebb5-106">Configuration</span></span>

<span data-ttu-id="6ebb5-107">テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="6ebb5-108">テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="6ebb5-109">この例`Order`では、の`DetailedOrder`サブセットを表します。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="6ebb5-110">必要な構成に加えて、を`Property(o => o.Status).HasColumnName("Status")`呼び出して`DetailedOrder.Status` 、と`Order.Status`同じ列にマップします。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> <span data-ttu-id="6ebb5-111">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="6ebb5-112">使用法</span><span class="sxs-lookup"><span data-stu-id="6ebb5-112">Usage</span></span>

<span data-ttu-id="6ebb5-113">テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-113">Saving and querying entities using table splitting is done in the same way as other entities.</span></span> <span data-ttu-id="6ebb5-114">EF Core 3.0 以降では、依存エンティティ参照をに`null`することができます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-114">And starting with EF Core 3.0 the dependent entity reference can be `null`.</span></span> <span data-ttu-id="6ebb5-115">依存エンティティ`NULL`によって使用されるすべての列がデータベースである場合は、クエリの実行時にインスタンスが作成されません。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-115">If all of the columns used by the dependent entity are `NULL` is the database then no instance for it will be created when queried.</span></span> <span data-ttu-id="6ebb5-116">また、すべてのプロパティが省略可能であり、がに`null`設定されている場合もありますが、これは想定されていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-116">This would also happen all of the properties are optional and set to `null`, which might not be expected.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="6ebb5-117">同時実行トークン</span><span class="sxs-lookup"><span data-stu-id="6ebb5-117">Concurrency tokens</span></span>

<span data-ttu-id="6ebb5-118">テーブルを共有するいずれかのエンティティ型が同時実行トークンを持っている場合は、同じテーブルにマップされたエンティティの1つだけが更新されたときに、古い同時実行トークンの値を回避するために、他のすべてのエンティティ型に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-118">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="6ebb5-119">コンシューマー側のコードにそれを公開しないようにするには、シャドウ状態で1つ作成することができます。</span><span class="sxs-lookup"><span data-stu-id="6ebb5-119">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
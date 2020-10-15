---
title: Microsoft SQL Server データベースプロバイダー-インデックス-EF Core
description: Entity Framework Core SQL Server プロバイダーに固有のインデックス機能
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: a01ab3d12f5bf5f05f0925c93d90c0ee40fe977c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061867"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="2a2bc-103">Entity Framework Core SQL Server プロバイダーに固有のインデックス機能</span><span class="sxs-lookup"><span data-stu-id="2a2bc-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="2a2bc-104">このページでは、SQL Server プロバイダーに固有のインデックス構成オプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="2a2bc-105">クラスタリング</span><span class="sxs-lookup"><span data-stu-id="2a2bc-105">Clustering</span></span>

<span data-ttu-id="2a2bc-106">クラスター化インデックスは、テーブルまたはビュー内のデータ行をそのキー値に基づいて並べ替え、格納します。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="2a2bc-107">テーブルに適切なクラスター化インデックスを作成すると、データは既に最適な順序でレイアウトされるため、クエリの速度が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="2a2bc-108">データ行自体は 1 つの順序でしか並べ替えられないので、1 つのテーブルに設定できるクラスター化インデックスは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="2a2bc-109">詳細については、 [クラスター化インデックスと非クラスター化インデックスに関する SQL Server のドキュメント](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="2a2bc-110">既定では、テーブルの主キー列はクラスター化インデックスによって暗黙的にバックアップされ、他のすべてのインデックスは非クラスター化されます。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="2a2bc-111">次のようにクラスター化するインデックスまたはキーを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a><span data-ttu-id="2a2bc-112">FILL FACTOR</span><span class="sxs-lookup"><span data-stu-id="2a2bc-112">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="2a2bc-113">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-113">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="2a2bc-114">インデックスのデータストレージとパフォーマンスを微調整するために、インデックスの fill factor オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-114">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="2a2bc-115">詳細については、 [FILL FACTOR に関する SQL Server のドキュメント](/sql/relational-databases/indexes/specify-fill-factor-for-an-index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-115">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="2a2bc-116">インデックスの FILL FACTOR は次のように構成できます。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-116">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="2a2bc-117">オンライン作成</span><span class="sxs-lookup"><span data-stu-id="2a2bc-117">Online creation</span></span>

<span data-ttu-id="2a2bc-118">[オンライン] オプションを使用すると、インデックスの作成中に、基になるテーブルまたはクラスター化インデックスデータ、および関連する非クラスター化インデックスへの同時ユーザーアクセスが可能になり、基になるデータの更新やクエリをユーザーが続行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-118">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="2a2bc-119">クラスター化インデックスの構築や再構築などのデータ定義言語 (DDL) 操作をオフラインで実行するときは、これらの操作により、基になるデータや関連付けられたインデックスに排他ロックがかけられます。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-119">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="2a2bc-120">詳細については、 [オンラインインデックスオプションの SQL Server に関するドキュメント](/sql/relational-databases/indexes/perform-index-operations-online)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-120">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="2a2bc-121">オンラインオプションを使用してインデックスを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2a2bc-121">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]

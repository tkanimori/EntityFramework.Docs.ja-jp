---
title: Microsoft SQL Server データベースプロバイダー-値の生成-EF Core
description: SQL Server Entity Framework Core データベースプロバイダーに固有の値生成パターン
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987138"
---
# <a name="sql-server-value-generation"></a><span data-ttu-id="a20a8-103">SQL Server 値の生成</span><span class="sxs-lookup"><span data-stu-id="a20a8-103">SQL Server Value Generation</span></span>

<span data-ttu-id="a20a8-104">このページでは、SQL Server プロバイダーに固有の値の生成構成とパターンについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a20a8-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="a20a8-105">最初に [ [値の生成] の [全般] ページ](xref:core/modeling/generated-properties)を読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a20a8-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="a20a8-106">IDENTITY 列</span><span class="sxs-lookup"><span data-stu-id="a20a8-106">IDENTITY columns</span></span>

<span data-ttu-id="a20a8-107">規則により、追加で生成される値を持つように構成されている数値列は [SQL SERVER id 列](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)として設定されます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="a20a8-108">シードと増分</span><span class="sxs-lookup"><span data-stu-id="a20a8-108">Seed and increment</span></span>

<span data-ttu-id="a20a8-109">既定では、ID 列は 1 (シード) から開始され、行が追加されるたびに1ずつ増加します (インクリメント)。</span><span class="sxs-lookup"><span data-stu-id="a20a8-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="a20a8-110">次のように、異なるシードと増分を構成できます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="a20a8-111">ID シードと増分値を構成する機能は EF Core 3.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="a20a8-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="a20a8-112">ID 列への明示的な値の挿入</span><span class="sxs-lookup"><span data-stu-id="a20a8-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="a20a8-113">既定では、SQL Server では、明示的な値を ID 列に挿入することはできません。</span><span class="sxs-lookup"><span data-stu-id="a20a8-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="a20a8-114">これを行うには `IDENTITY_INSERT` 、を呼び出す前に、次のようにを手動で有効にする必要があり `SaveChanges()` ます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="a20a8-115">SQL Server プロバイダー内で自動的にこれを行うための[機能要求](https://github.com/aspnet/EntityFramework/issues/703)が、バックログに用意されています。</span><span class="sxs-lookup"><span data-stu-id="a20a8-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="a20a8-116">シーケンス</span><span class="sxs-lookup"><span data-stu-id="a20a8-116">Sequences</span></span>

<span data-ttu-id="a20a8-117">ID 列の代わりに、標準シーケンスを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="a20a8-118">これは、さまざまなシナリオで役に立ちます。たとえば、複数の列で1つのシーケンスから既定値を描画することができます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="a20a8-119">SQL Server を使用すると、シーケンスを作成し、 [シーケンスの [全般] ページ](xref:core/modeling/sequences)で詳しく説明されているように使用できます。</span><span class="sxs-lookup"><span data-stu-id="a20a8-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="a20a8-120">でシーケンスを使用するようにプロパティを構成する必要が `HasDefaultValueSql()` あります。</span><span class="sxs-lookup"><span data-stu-id="a20a8-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>

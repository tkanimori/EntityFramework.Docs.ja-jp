---
title: EF Core 5.0 での破壊的変更 - EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666169"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="f231b-102">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="f231b-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="f231b-103">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f231b-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="f231b-104">まとめ</span><span class="sxs-lookup"><span data-stu-id="f231b-104">Summary</span></span>

| <span data-ttu-id="f231b-105">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="f231b-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="f231b-106">**影響**</span><span class="sxs-lookup"><span data-stu-id="f231b-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="f231b-107">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="f231b-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="f231b-108">Low</span><span class="sxs-lookup"><span data-stu-id="f231b-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="f231b-109">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="f231b-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="f231b-110">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="f231b-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="f231b-111">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f231b-111">**Old behavior**</span></span>

<span data-ttu-id="f231b-112">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="f231b-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="f231b-113">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="f231b-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="f231b-114">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="f231b-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="f231b-115">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f231b-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="f231b-116">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f231b-116">**New behavior**</span></span>

<span data-ttu-id="f231b-117">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f231b-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="f231b-118">これは、SpatiaLite の AddGeometryColumn 関数の基になる動作と厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="f231b-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="f231b-119">**理由**</span><span class="sxs-lookup"><span data-stu-id="f231b-119">**Why**</span></span>

<span data-ttu-id="f231b-120">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="f231b-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="f231b-121">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f231b-121">**Mitigations**</span></span>

<span data-ttu-id="f231b-122">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="f231b-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

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
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 での破壊的変更

次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。

## <a name="summary"></a>まとめ

| **重大な変更**                                                                                                               | **影響** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Low        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました

[イシュー #14257 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**以前の動作**

HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。 ただし、この影響を受けていたのはデータベースの作成だけです。 追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。 また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。

**新しい動作**

追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。 これは、SpatiaLite の AddGeometryColumn 関数の基になる動作と厳密に一致します。

**理由**

列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。

**軽減策**

`HasColumnType` を使用してディメンションを指定します。

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

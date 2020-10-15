---
title: Microsoft SQL Server データベースプロバイダー-空間データ-EF Core
description: Entity Framework Core Microsoft SQL Server データベースプロバイダーでの空間データの使用
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066552"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>SQL Server EF Core プロバイダーの空間データ

このページには、Microsoft SQL Server データベースプロバイダーでの空間データの使用に関する追加情報が含まれています。 EF Core での空間データの使用に関する一般的な情報については、主な [空間データ](xref:core/modeling/spatial) のドキュメントを参照してください。

## <a name="geography-or-geometry"></a>Geography または geometry

既定では、空間プロパティは SQL Server の列にマップされ `geography` ます。 を使用するには `geometry` 、モデルの [列の型を構成](xref:core/modeling/entity-properties#column-data-types) します。

## <a name="geography-polygon-rings"></a>Geography polygon リング

列の型を使用する場合、SQL Server によって `geography` 、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。 外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) は、値をデータベースに送信する前に検証します。

## <a name="fullglobe"></a>FullGlobe

SQL Server には、列の型を使用するときに全地球を表す非標準の geometry 型があり `geography` ます。 また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。 これらのいずれも、NTS ではサポートされていません。

> [!WARNING]
> NTS では、これに基づく FullGlobe と多角形はサポートされていません。

## <a name="curves"></a>曲線

主な [空間データ](xref:core/modeling/spatial) のドキュメントで説明したように、NTS は現在、曲線を表すことはできません。 これは、EF Core で使用する前に、 [STCurveToLine](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type) メソッドを使用して CircularString、CompoundCurve、および CurePolygon の値を変換する必要があることを意味します。

> [!WARNING]
> CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。

## <a name="spatial-function-mappings"></a>空間関数のマッピング

次の表は、どの NTS メンバーがどの SQL 関数に変換されるかを示しています。 変換は、列の型が geography または geometry のどちらであるかによって異なります。

.NET                                      | SQL (geography)                                              | SQL (geometry)
----------------------------------------- | ------------------------------------------------------------ | --------------
geometry.領域                             | @geometry.STArea()                                           | @geometry.STArea()
geometry.AsBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometry.AsText ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometry.範囲                         |                                                              | @geometry.STBoundary()
geometry.バッファー (距離)                 | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
geometry.重心                         |                                                              | @geometry.STCentroid()
geometry.(G) を含む                      | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
geometry.ConvexHull()                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
geometry.交差 (g)                       |                                                              | @geometry.STCrosses(@g)
geometry.差 (その他)                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
geometry.多次元                        | @geometry.STDimension()                                      | @geometry.STDimension()
geometry.不整合 (g)                      | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
geometry.距離 (g)                      | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
geometry.差出人                         |                                                              | @geometry.STEnvelope()
geometry.EqualsTopologically (g)           | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
geometry.GeometryType                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
geometry.GetGeometryN (n)                  | @geometry.STGeometryN( @n + 1)                                | @geometry.STGeometryN( @n + 1)
geometry.InteriorPoint                    |                                                              | @geometry.STPointOnSurface()
geometry.共通部分 (その他)              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
geometry.交差 (g)                    | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
geometry.IsEmpty                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
geometry.IsSimple                         |                                                              | @geometry.STIsSimple()
geometry.IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
geometry.Iswithin Distance (ジオメトリ、距離) | @geometry.STDistance( @geom ) <= @distance                     | @geometry.STDistance( @geom ) <= @distance
geometry.数                           | @geometry.STLength()                                         | @geometry.STLength()
geometry.NumGeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
geometry.X.numpoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
geometry.OgcGeometryType                  | CASE @geometry.STGeometryType () WHEN N'Point ' THEN 1...終わり | CASE @geometry.STGeometryType () WHEN N'Point ' THEN 1...終わり
geometry.重複 (g)                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
geometry.PointOnSurface                   |                                                              | @geometry.STPointOnSurface()
geometry.関連付け (g, intersectionPattern)   |                                                              | @geometry.STRelate(@g, @intersectionPattern)
geometry.SRID                             | @geometry.STSrid                                             | @geometry.STSrid
geometry.SymmetricDifference (その他)       | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
geometry.ToBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometry.ToText ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometry.接触 (g)                       |                                                              | @geometry.STTouches(@g)
geometry.共用体 (その他)                     | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
geometry.内側 (g)                        | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
geometryCollection [i]                     | @geometryCollection.STGeometryN( @i + 1)                      | @geometryCollection.STGeometryN( @i + 1)
geometryCollection                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
lineString                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
lineString                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
lineString n (n)                   | @lineString.STPointN( @n + 1)                                 | @lineString.STPointN( @n + 1)
lineString                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
lineString                         |                                                              | @lineString.IsRing()
lineString StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
multiLineString                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
視点.M                                   | @point.M                                                     | @point.M
視点.閉じる                                   | @point.Long                                                  | @point.STX
視点.前年                                   | @point.Lat                                                   | @point.STY
視点.方向                                   | @point.Z                                                     | @point.Z
ポリゴン.ExteriorRing                      | @polygon.RingNスナップショット                                            | @polygon.STExteriorRing()
ポリゴン.GetInteriorRingN (n)               | @polygon.RingN( @n + 2)                                       | @polygon.STInteriorRingN( @n + 1)
ポリゴン.NumInteriorRings                  | @polygon.NumRings()-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>その他の資料

* [SQL Server の空間データ](/sql/relational-databases/spatial/spatial-data-sql-server)
* [NetTopologySuite のドキュメント](https://nettopologysuite.github.io/NetTopologySuite/)

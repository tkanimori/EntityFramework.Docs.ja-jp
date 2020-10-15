---
title: SQLite データベースプロバイダー-空間データ-EF Core
description: Entity Framework Core SQLite データベースプロバイダーでの空間データの使用
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066525"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>SQLite EF Core プロバイダーの空間データ

このページには、SQLite データベースプロバイダーでの空間データの使用に関する追加情報が含まれています。 EF Core での空間データの使用に関する一般的な情報については、主な [空間データ](xref:core/modeling/spatial) のドキュメントを参照してください。

## <a name="installing-spatialite"></a>SpatiaLite のインストール

Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。 他のプラットフォームでは、別のプラットフォームをインストールする必要があります。 通常、これはソフトウェアパッケージマネージャーを使用して行います。 たとえば、Debian および Ubuntu で APT を使用できます。MacOS での Homebrew です。

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

残念ながら、新しいバージョンの PROJ (SpatiaLite の依存関係) は EF の既定の [SQLitePCLRaw バンドル](/dotnet/standard/data/sqlite/custom-versions#bundles)と互換性がありません。 この問題を回避するには、代わりにシステム SQLite ライブラリを使用します。

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

**MacOS**では、Homebrew のバージョンの SQLite を使用するように、アプリを実行する前に環境変数を設定する必要もあります。 Visual Studio for Mac では、これを [プロジェクト > プロジェクトオプション] の下に設定して **> 構成を実行 > > 既定値**

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>SRID の構成

SpatiaLite では、列ごとに SRID を指定する必要があります。 既定の SRID は `0` です。 ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> 4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。

## <a name="dimension"></a>Dimension

列の既定のディメンション (座標) は、X と Y です。Z や M などの追加の座標を有効にするには、列の型を構成します。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>空間関数のマッピング

次の表は、どの [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) メンバーがどの SQL 関数に変換されるかを示しています。

.NET                                        | SQL
------------------------------------------- | ---
geometry.領域                               | 区分 ( @geometry )
geometry.AsBinary ()                         | AsBinary ( @geometry )
geometry.AsText ()                           | AsText ( @geometry )
geometry.範囲                           | 境界 ( @geometry )
geometry.バッファー (距離)                   | Buffer ( @geometry , @distance )
geometry.Buffer (distance, quadrantSegments) | Buffer ( @geometry , @distance , @quadrantSegments )
geometry.重心                           | 重心 ( @geometry )
geometry.(G) を含む                        | Contains ( @geometry , @g )
geometry.ConvexHull()                       | ConvexHull ( @geometry )
geometry.(G)                       | ( @geometry 、 @g )
geometry.カバー (g)                          | カバー ( @geometry 、 @g )
geometry.交差 (g)                         | ( @geometry ,) を交差する @g
geometry.差 (その他)                  | 相違点 ( @geometry 、 @other )
geometry.多次元                          | Dimension ( @geometry )
geometry.不整合 (g)                        | 不整合 ( @geometry 、 @g )
geometry.距離 (g)                        | Distance ( @geometry , @g )
geometry.差出人                           | Envelope ( @geometry )
geometry.EqualsTopologically (g)             | Equals ( @geometry , @g )
geometry.GeometryType                       | GeometryType ( @geometry )
geometry.GetGeometryN (n)                    | GeometryN ( @geometry , @n + 1)
geometry.InteriorPoint                      | PointOnSurface ( @geometry )
geometry.共通部分 (その他)                | 積集合 ( @geometry , @other )
geometry.交差 (g)                      | ( @geometry ,) と交差します @g 。
geometry.IsEmpty                            | IsEmpty ( @geometry )
geometry.IsSimple                           | IsSimple ( @geometry )
geometry.IsValid                            | IsValid ( @geometry )
geometry.Iswithin Distance (ジオメトリ、距離)   | Distance ( @geometry , @geom ) <= @distance
geometry.数                             | GLength ( @geometry )
geometry.NumGeometries                      | NumGeometries ( @geometry )
geometry.X.numpoints                          | NumPoints ( @geometry )
geometry.OgcGeometryType                    | CASE GeometryType ( @geometry ) の場合、' POINT '、1...終わり
geometry.重複 (g)                        | 重複 ( @geometry , @g )
geometry.PointOnSurface                     | PointOnSurface ( @geometry )
geometry.関連付け (g, intersectionPattern)     | 関連付け ( @geometry , @g , @intersectionPattern )
geometry.Reverse ()                          | ST_Reverse ( @geometry )
geometry.SRID                               | SRID ( @geometry )
geometry.SymmetricDifference (その他)         | SymDifference ( @geometry , @other )
geometry.ToBinary ()                         | AsBinary ( @geometry )
geometry.ToText ()                           | AsText ( @geometry )
geometry.接触 (g)                         | ( @geometry 、) をタッチします @g 。
geometry.Union ()                            | UnaryUnion ( @geometry )
geometry.共用体 (その他)                       | GUnion ( @geometry , @other )
geometry.内側 (g)                          | 内 ( @geometry 、 @g )
geometryCollection [i]                       | GeometryN ( @geometryCollection , @i + 1)
geometryCollection                    | NumGeometries ( @geometryCollection )
lineString                            | NumPoints ( @lineString )
lineString                         | エンドポイント ( @lineString )
lineString n (n)                     | PointN ( @lineString , @n + 1)
lineString                         | IsClosed ( @lineString )
lineString                           | IsRing ( @lineString )
lineString StartPoint                       | StartPoint ( @lineString )
multiLineString                    | IsClosed ( @multiLineString )
視点.M                                     | M ( @point )
視点.閉じる                                     | X ( @point )
視点.前年                                     | Y ( @point )
視点.方向                                     | Z ( @point )
ポリゴン.ExteriorRing                        | ExteriorRing ( @polygon )
ポリゴン.GetInteriorRingN (n)                 | InteriorRingN ( @polygon , @n + 1)
ポリゴン.NumInteriorRings                    | NumInteriorRing ( @polygon )

## <a name="additional-resources"></a>その他の資料

* [SpatiaLite ホームページ](https://www.gaia-gis.it/fossil/libspatialite)
* [NetTopologySuite のドキュメント](https://nettopologysuite.github.io/NetTopologySuite/)

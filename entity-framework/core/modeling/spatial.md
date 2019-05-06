---
title: 空間データ - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cf488c6b7d94ca19018efe1c23ff410fe7eb594b
ms.sourcegitcommit: 81c53ac43d8f15b900f117294ec71dc49fe028fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817911"
---
# <a name="spatial-data"></a>空間データ

> [!NOTE]
> この機能は、EF Core 2.2 の新機能です。

空間データは、物理的な場所とオブジェクトの形状を表します。 多くのデータベースは、インデックスを作成し、その他のデータと併せてクエリを実行できるように、この種類のデータのサポートを提供します。 一般的なシナリオにはの場所から指定した距離内のオブジェクトの照会や境界には、特定の場所が含まれています。 オブジェクトの選択が含まれます。 EF Core を使用して空間データ型マッピングをサポートする、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリ。

## <a name="installing"></a>インストール

EF Core の空間データを使用するには、適切なサポートの NuGet パッケージをインストールする必要があります。 インストールする必要があると、パッケージは、使用しているプロバイダーに依存します。

EF Core プロバイダー                        | 空間の NuGet パッケージ
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>リバース エンジニア リング

空間の NuGet パッケージのも有効にする[リバース エンジニア リング](../managing-schemas/scaffolding.md)が空間のプロパティを使用したモデルは、パッケージをインストールする必要があります***する前に***を実行している`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`します。 ない場合は、列の型のマッピングを検出していないに関する警告が表示され、列はスキップされます。

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite は、.NET の空間ライブラリです。 EF Core では、モデルの NTS 型を使用して、空間データをデータベースで型のマッピングを使用できます。

NTS 経由での空間型へのマッピングを有効にするには、プロバイダーの DbContext オプション ビルダーを UseNetTopologySuite メソッドを呼び出します。 たとえば、SQL Server で、次のように呼び出すことは。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

いくつかの空間データの種類があります。 使用する種類を許可する図形の種類によって異なります。 モデルのプロパティを使用できる NTS 型の階層を次に示します。 内にある、`NetTopologySuite.Geometries`名前空間。 GeoAPI パッケージ内の対応するインターフェイス (`GeoAPI.Geometries`名前空間) こともできます。

* geometry
  * ポイント
  * LineString
  * 多角形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString、CompoundCurve、および CurePolygon は NTS によってサポートされていません。

基本の Geometry 型を使用すると、どの種類のプロパティで指定する図形ができます。

次のエンティティ クラスを使用して、内のテーブルにマップする可能性があります、 [Wide World Importers サンプル データベース](http://go.microsoft.com/fwlink/?LinkID=800630)します。

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public IPoint Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public IGeometry Border { get; set; }
}
```

### <a name="creating-values"></a>値を作成します。

Geometry オブジェクトを作成するのにコンストラクターを使用できます。ただし、NTS では、geometry ファクトリを代わりに使用することをお勧めします。 これは、既定の SRID (使用する、座標空間参照系) を指定することができ、できます (計算中に使用)、有効桁数のモデルと (どの座標--ディメンションを決定する座標のシーケンスなどのより高度な制御メジャーを利用)。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> 4326 は、wgs-84、GPS およびその他の地理的なシステムで使用される標準を参照します。

### <a name="longitude-and-latitude"></a>緯度と経度

NTS で座標がの観点では X と Y の値。 経度と緯度を表現するには、緯度経度と Y の X を使用します。 これは**下位**から、`latitude, longitude`形式は通常、これらの値を確認します。

### <a name="srid-ignored-during-client-operations"></a>クライアントの操作中に SRID が無視されます。

NTS は、操作中に、SRID 値を無視します。 平面座標系が想定しています。 つまり、経度と緯度、距離、長さ、および領域がないメーターの角度でになりますようないくつかのクライアントで評価される値の観点からの座標を指定する場合。 意味のある値は、まずにプロジェクトの座標などのライブラリを使用して、もう 1 つの座標系を[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)これらの値を計算する前にします。

操作が SQL 経由での EF Core によってサーバーで評価される場合は、結果のユニットは、データベースによって決定されます。

ProjNet4GeoAPI を使用して、2 つの都市間の距離を計算する例を示します。

``` csharp
static class GeometryExtensions
{
    static readonly IGeometryServices _geometryServices = NtsGeometryServices.Instance;
    static readonly ICoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                // (3857 and 4326 included automatically)

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static IGeometry ProjectTo(this IGeometry geometry, int srid)
    {
        var geometryFactory = _geometryServices.CreateGeometryFactory(srid);
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        return GeometryTransform.TransformGeometry(
            geometryFactory,
            geometry,
            transformation.MathTransform);
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>データのクエリ

LINQ では、NTS メソッドとデータベース機能として使用できるプロパティは SQL に変換されます。 たとえば、距離と Contains メソッドは、次のクエリに変換されます。 この記事の最後にある表では、さまざまな EF Core プロバイダーによってサポートされるメンバーを示します。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

SQL Server を使用している場合の注意すべき点があります。

### <a name="geography-or-geometry"></a>地理またはジオメトリ

既定では、空間プロパティのマップ`geography`SQL Server 内の列。 使用する`geometry`、[列の型を構成する](xref:core/modeling/relational/data-types)モデル。

### <a name="geography-polygon-rings"></a>地理の多角形のリング

使用する場合、`geography`列型、SQL Server が外部リング (またはシェル) に追加の要件を課すと内部リング (穴)。 外部リング指向左回りには、内部リングを時計回りにします。 NTS は、値をデータベースに送信する前に、これを検証します。

### <a name="fullglobe"></a>FullGlobe

SQL Server が非標準の geometry 型を使用する場合は、全球を表す、`geography`列の型。 全球 (せずに、外部リング) に基づく多角形を表す方法もあります。 どちらもは NTS でサポートされます。

> [!WARNING]
> FullGlobe とそれに基づく多角形は、NTS でサポートされていません。

## <a name="sqlite"></a>SQLite

SQLite を使用する場合のいくつか追加の情報を次に示します。

### <a name="installing-spatialite"></a>SpatiaLite をインストールします。

、Windows でネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。 その他のプラットフォームでは、別途インストールする必要があります。 これは、通常のソフトウェアのパッケージ マネージャーを使用します。 たとえば、Ubuntu または macos の Homebrew での APT を使用できます。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a>SRID の構成

SpatiaLite、列を 1 列あたり SRID を指定する必要があります。 既定の SRID は`0`します。 ForSqliteHasSrid メソッドを使用して異なる SRID を指定します。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>ディメンション

SRID と同様に、列のディメンション (または座標) がも指定されて、列の一部として。 既定の座標は X と適用を有効にする追加の座標 (z 座標および M) が ForSqliteHasDimension メソッドを使用します。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>翻訳済みの操作

次の表では、どの NTS メンバーは、EF Core プロバイダーによって SQL に変換を示します。

NetTopologySuite | SQL Server (geometry) | SQL Server (geography) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry.Area | ✔ | ✔ | ✔ | ✔
Geometry.AsBinary() | ✔ | ✔ | ✔ | ✔
Geometry.AsText() | ✔ | ✔ | ✔ | ✔
Geometry.Boundary | ✔ | | ✔ | ✔
Geometry.Buffer(double) | ✔ | ✔ | ✔ | ✔
Geometry.Buffer (int、double) | | | ✔
Geometry.Centroid | ✔ | | ✔ | ✔
Geometry.Contains(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.ConvexHull() | ✔ | ✔ | ✔ | ✔
Geometry.CoveredBy(Geometry) | | | ✔ | ✔
Geometry.Covers(Geometry) | | | ✔ | ✔
Geometry.Crosses(Geometry) | ✔ | | ✔ | ✔
Geometry.Difference(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Dimension | ✔ | ✔ | ✔ | ✔
Geometry.Disjoint(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Distance(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Envelope | ✔ | | ✔ | ✔
Geometry.EqualsExact(Geometry) | | | | ✔
Geometry.EqualsTopologically(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.GeometryType | ✔ | ✔ | ✔ | ✔
Geometry.GetGeometryN(int) | ✔ | | ✔ | ✔
Geometry.InteriorPoint | ✔ | | ✔
Geometry.Intersection(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Intersects(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry.IsSimple | ✔ | | ✔ | ✔
Geometry.IsValid | ✔ | ✔ | ✔ | ✔
Geometry.IsWithinDistance (Geometry、double) | ✔ | | ✔
Geometry.Length | ✔ | ✔ | ✔ | ✔
Geometry.NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry.NumPoints | ✔ | ✔ | ✔ | ✔
Geometry.OgcGeometryType | ✔ | ✔ | ✔
Geometry.Overlaps(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.PointOnSurface | ✔ | | ✔ | ✔
Geometry.Relate (Geometry、文字列) | ✔ | | ✔ | ✔
Geometry.Reverse() | | | ✔ | ✔
Geometry.SRID | ✔ | ✔ | ✔ | ✔
Geometry.SymmetricDifference(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.ToBinary() | ✔ | ✔ | ✔ | ✔
Geometry.ToText() | ✔ | ✔ | ✔ | ✔
Geometry.Touches(Geometry) | ✔ | | ✔ | ✔
Geometry.Union() | | | ✔
Geometry.Union(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Within(Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection.Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString.Count | ✔ | ✔ | ✔ | ✔
LineString.EndPoint | ✔ | ✔ | ✔ | ✔
LineString.GetPointN(int) | ✔ | ✔ | ✔ | ✔
LineString.IsClosed | ✔ | ✔ | ✔ | ✔
LineString.IsRing | ✔ | | ✔ | ✔
LineString.StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString.IsClosed | ✔ | ✔ | ✔ | ✔
Point.M | ✔ | ✔ | ✔ | ✔
Point.X | ✔ | ✔ | ✔ | ✔
Point.Y | ✔ | ✔ | ✔ | ✔
Point.Z | ✔ | ✔ | ✔ | ✔
Polygon.ExteriorRing | ✔ | ✔ | ✔ | ✔
Polygon.GetInteriorRingN(int) | ✔ | ✔ | ✔ | ✔
Polygon.NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>その他の技術情報

* [SQL Server での空間データ](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite ホームページ](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空間ドキュメント](http://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS ドキュメント](http://postgis.net/documentation/)

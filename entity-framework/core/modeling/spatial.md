---
title: 空間データ-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 8dae1ab949c77ffa08904b12a5716b729e6913a1
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502241"
---
# <a name="spatial-data"></a>空間データ

> [!NOTE]
> この機能は EF Core 2.2 で追加されました。

空間データは、オブジェクトの物理的な位置と形状を表します。 多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。 一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。 EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリを使用した空間データ型へのマッピングがサポートされています。

## <a name="installing"></a>のインストール

EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。 どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。

EF Core プロバイダー                        | 空間 NuGet パッケージ
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [NetTopologySuite (Microsoft EntityFrameworkCore)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>リバース エンジニアリング

空間 NuGet パッケージでは、空間プロパティでモデルの[リバースエンジニアリング](../managing-schemas/scaffolding.md)を行うこともできますが、`Scaffold-DbContext` または `dotnet ef dbcontext scaffold`を実行する***前***にパッケージをインストールする必要があります。 そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite は、.NET 用の空間ライブラリです。 EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。

NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。 たとえば、SQL Server では、次のようにを呼び出します。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

空間データ型はいくつかあります。 使用する種類は、許可する図形の種類によって異なります。 ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。 これらは `NetTopologySuite.Geometries` 名前空間内にあります。

* ジオメトリ
  * ポイント
  * LineString
  * 多角形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。

基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。

次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>値の作成

コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。 これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用) や座標シーケンス (次元を決定する) など、より高度なものを制御できます。およびメジャーが使用可能)。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> 4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。

### <a name="longitude-and-latitude"></a>経度と緯度

NTS 内の座標は、X 値と Y 値で表現されます。 経度と緯度を表すには、経度には X、緯度には Y を使用します。 これは、通常、これらの値が表示される `latitude, longitude` 形式とは**逆**になっていることに注意してください。

### <a name="srid-ignored-during-client-operations"></a>クライアント操作中に SRID が無視されました

NTS は、操作中に SRID の値を無視します。 平面座標系を前提としています。 これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。 意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。

操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。

ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

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

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>データのクエリ

LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。 たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。 この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

SQL Server を使用している場合は、注意が必要な点がいくつかあります。

### <a name="geography-or-geometry"></a>Geography または geometry

既定では、空間プロパティは SQL Server の `geography` 列にマップされます。 `geometry`を使用するには、モデルの[列の型を構成](xref:core/modeling/entity-properties#column-data-types)します。

### <a name="geography-polygon-rings"></a>Geography polygon リング

`geography` 列の型を使用する場合、SQL Server によって、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。 外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。 NTS は、データベースに値を送信する前にこのことを検証します。

### <a name="fullglobe"></a>FullGlobe

`geography` 列の型を使用する場合、SQL Server には、全地球を表す非標準の geometry 型があります。 また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。 これらのいずれも、NTS ではサポートされていません。

> [!WARNING]
> NTS では、これに基づく FullGlobe と多角形はサポートされていません。

## <a name="sqlite"></a>SQLite

SQLite を使用する場合の追加情報を次に示します。

### <a name="installing-spatialite"></a>SpatiaLite のインストール

Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。 他のプラットフォームでは、別のプラットフォームをインストールする必要があります。 通常、これはソフトウェアパッケージマネージャーを使用して行います。 たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a>SRID の構成

SpatiaLite では、列ごとに SRID を指定する必要があります。 既定の SRID は `0`です。 ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>ディメンション

SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。 既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>翻訳された操作

次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。

NetTopologySuite | SQL Server (geometry) | SQL Server (geography) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry. 面 | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary () | ✔ | ✔ | ✔ | ✔
Geometry. AsText () | ✔ | ✔ | ✔ | ✔
Geometry. 境界 | ✔ | | ✔ | ✔
Geometry. Buffer (double) | ✔ | ✔ | ✔ | ✔
Geometry. Buffer (double, int) | | | ✔ | ✔
Geometry. 重心 | ✔ | | ✔ | ✔
Geometry. Contains (Geometry) | ✔ | ✔ | ✔ | ✔
ConvexHull () | ✔ | ✔ | ✔ | ✔
Geometry (Geometry) | | | ✔ | ✔
Geometry. カバー (Geometry) | | | ✔ | ✔
Geometry. 交差 (Geometry) | ✔ | | ✔ | ✔
Geometry. 差 (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ディメンション | ✔ | ✔ | ✔ | ✔
Geometry. 不整合 (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Distance (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. エンベロープ | ✔ | | ✔ | ✔
EqualsExact (Geometry) | | | | ✔
EqualsTopologically (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryType | ✔ | ✔ | ✔ | ✔
GetGeometryN (int) | ✔ | | ✔ | ✔
InteriorPoint | ✔ | | ✔ | ✔
Geometry. 積集合 (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. 交差 (Geometry) | ✔ | ✔ | ✔ | ✔
IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. IsSimple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
Geometry. Iswithin Distance (Geometry, double) | ✔ | | ✔ | ✔
Geometry. Length | ✔ | ✔ | ✔ | ✔
Geometry. NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry. NumPoints | ✔ | ✔ | ✔ | ✔
OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry. 重なり (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. PointOnSurface | ✔ | | ✔ | ✔
Geometry. 関連付け (Geometry, string) | ✔ | | ✔ | ✔
Geometry. Reverse () | | | ✔ | ✔
SRID | ✔ | ✔ | ✔ | ✔
Geometry. SymmetricDifference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ToBinary () | ✔ | ✔ | ✔ | ✔
Geometry. ToText () | ✔ | ✔ | ✔ | ✔
Geometry (Geometry) | ✔ | | ✔ | ✔
Geometry. Union () | | | ✔ | ✔
Geometry. 共用体 (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. 内 (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString | ✔ | ✔ | ✔ | ✔
LineString | ✔ | ✔ | ✔ | ✔
LineString n (int) | ✔ | ✔ | ✔ | ✔
LineString | ✔ | ✔ | ✔ | ✔
LineString | ✔ | | ✔ | ✔
LineString StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString | ✔ | ✔ | ✔ | ✔
Point. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Point. Y | ✔ | ✔ | ✔ | ✔
Point. Z | ✔ | ✔ | ✔ | ✔
ExteriorRing | ✔ | ✔ | ✔ | ✔
GetInteriorRingN (int) | ✔ | ✔ | ✔ | ✔
NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>その他の技術情報

* [SQL Server の空間データ](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite ホームページ](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空間のドキュメント](https://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS のドキュメント](https://postgis.net/documentation/)

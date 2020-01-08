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
# <a name="spatial-data"></a><span data-ttu-id="8f2a6-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="8f2a6-103">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="8f2a6-104">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="8f2a6-105">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="8f2a6-106">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="8f2a6-107">EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="8f2a6-108">のインストール</span><span class="sxs-lookup"><span data-stu-id="8f2a6-108">Installing</span></span>

<span data-ttu-id="8f2a6-109">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="8f2a6-110">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="8f2a6-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8f2a6-111">EF Core Provider</span></span>                        | <span data-ttu-id="8f2a6-112">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="8f2a6-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="8f2a6-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="8f2a6-114">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="8f2a6-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="8f2a6-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="8f2a6-116">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="8f2a6-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="8f2a6-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="8f2a6-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8f2a6-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="8f2a6-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="8f2a6-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="8f2a6-120">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8f2a6-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="8f2a6-121">リバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="8f2a6-121">Reverse engineering</span></span>

<span data-ttu-id="8f2a6-122">空間 NuGet パッケージでは、空間プロパティでモデルの[リバースエンジニアリング](../managing-schemas/scaffolding.md)を行うこともできますが、`Scaffold-DbContext` または `dotnet ef dbcontext scaffold`を実行する***前***にパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="8f2a6-123">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="8f2a6-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="8f2a6-125">NetTopologySuite は、.NET 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="8f2a6-126">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="8f2a6-127">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="8f2a6-128">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="8f2a6-129">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-129">There are several spatial data types.</span></span> <span data-ttu-id="8f2a6-130">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="8f2a6-131">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="8f2a6-132">これらは `NetTopologySuite.Geometries` 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="8f2a6-133">ジオメトリ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-133">Geometry</span></span>
  * <span data-ttu-id="8f2a6-134">ポイント</span><span class="sxs-lookup"><span data-stu-id="8f2a6-134">Point</span></span>
  * <span data-ttu-id="8f2a6-135">LineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-135">LineString</span></span>
  * <span data-ttu-id="8f2a6-136">多角形</span><span class="sxs-lookup"><span data-stu-id="8f2a6-136">Polygon</span></span>
  * <span data-ttu-id="8f2a6-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="8f2a6-137">GeometryCollection</span></span>
    * <span data-ttu-id="8f2a6-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="8f2a6-138">MultiPoint</span></span>
    * <span data-ttu-id="8f2a6-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-139">MultiLineString</span></span>
    * <span data-ttu-id="8f2a6-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="8f2a6-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="8f2a6-141">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="8f2a6-142">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="8f2a6-143">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="8f2a6-144">値の作成</span><span class="sxs-lookup"><span data-stu-id="8f2a6-144">Creating values</span></span>

<span data-ttu-id="8f2a6-145">コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="8f2a6-146">これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用) や座標シーケンス (次元を決定する) など、より高度なものを制御できます。およびメジャーが使用可能)。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="8f2a6-147">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="8f2a6-148">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="8f2a6-148">Longitude and Latitude</span></span>

<span data-ttu-id="8f2a6-149">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="8f2a6-150">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="8f2a6-151">これは、通常、これらの値が表示される `latitude, longitude` 形式とは**逆**になっていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="8f2a6-152">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="8f2a6-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="8f2a6-153">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="8f2a6-154">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="8f2a6-155">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="8f2a6-156">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="8f2a6-157">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="8f2a6-158">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="8f2a6-159">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-159">Querying Data</span></span>

<span data-ttu-id="8f2a6-160">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="8f2a6-161">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="8f2a6-162">この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="8f2a6-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8f2a6-163">SQL Server</span></span>

<span data-ttu-id="8f2a6-164">SQL Server を使用している場合は、注意が必要な点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="8f2a6-165">Geography または geometry</span><span class="sxs-lookup"><span data-stu-id="8f2a6-165">Geography or geometry</span></span>

<span data-ttu-id="8f2a6-166">既定では、空間プロパティは SQL Server の `geography` 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="8f2a6-167">`geometry`を使用するには、モデルの[列の型を構成](xref:core/modeling/entity-properties#column-data-types)します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="8f2a6-168">Geography polygon リング</span><span class="sxs-lookup"><span data-stu-id="8f2a6-168">Geography polygon rings</span></span>

<span data-ttu-id="8f2a6-169">`geography` 列の型を使用する場合、SQL Server によって、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="8f2a6-170">外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="8f2a6-171">NTS は、データベースに値を送信する前にこのことを検証します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="8f2a6-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="8f2a6-172">FullGlobe</span></span>

<span data-ttu-id="8f2a6-173">`geography` 列の型を使用する場合、SQL Server には、全地球を表す非標準の geometry 型があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="8f2a6-174">また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="8f2a6-175">これらのいずれも、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="8f2a6-176">NTS では、これに基づく FullGlobe と多角形はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="8f2a6-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="8f2a6-177">SQLite</span></span>

<span data-ttu-id="8f2a6-178">SQLite を使用する場合の追加情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="8f2a6-179">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="8f2a6-179">Installing SpatiaLite</span></span>

<span data-ttu-id="8f2a6-180">Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="8f2a6-181">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="8f2a6-182">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="8f2a6-183">たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="8f2a6-184">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="8f2a6-184">Configuring SRID</span></span>

<span data-ttu-id="8f2a6-185">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="8f2a6-186">既定の SRID は `0`です。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-186">The default SRID is `0`.</span></span> <span data-ttu-id="8f2a6-187">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="8f2a6-188">ディメンション</span><span class="sxs-lookup"><span data-stu-id="8f2a6-188">Dimension</span></span>

<span data-ttu-id="8f2a6-189">SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="8f2a6-190">既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="8f2a6-191">翻訳された操作</span><span class="sxs-lookup"><span data-stu-id="8f2a6-191">Translated Operations</span></span>

<span data-ttu-id="8f2a6-192">次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="8f2a6-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="8f2a6-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8f2a6-193">NetTopologySuite</span></span> | <span data-ttu-id="8f2a6-194">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-194">SQL Server (geometry)</span></span> | <span data-ttu-id="8f2a6-195">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-195">SQL Server (geography)</span></span> | <span data-ttu-id="8f2a6-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="8f2a6-196">SQLite</span></span> | <span data-ttu-id="8f2a6-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="8f2a6-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="8f2a6-198">Geometry. 面</span><span class="sxs-lookup"><span data-stu-id="8f2a6-198">Geometry.Area</span></span> | <span data-ttu-id="8f2a6-199">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-199">✔</span></span> | <span data-ttu-id="8f2a6-200">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-200">✔</span></span> | <span data-ttu-id="8f2a6-201">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-201">✔</span></span> | <span data-ttu-id="8f2a6-202">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-202">✔</span></span>
<span data-ttu-id="8f2a6-203">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="8f2a6-204">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-204">✔</span></span> | <span data-ttu-id="8f2a6-205">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-205">✔</span></span> | <span data-ttu-id="8f2a6-206">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-206">✔</span></span> | <span data-ttu-id="8f2a6-207">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-207">✔</span></span>
<span data-ttu-id="8f2a6-208">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-208">Geometry.AsText()</span></span> | <span data-ttu-id="8f2a6-209">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-209">✔</span></span> | <span data-ttu-id="8f2a6-210">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-210">✔</span></span> | <span data-ttu-id="8f2a6-211">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-211">✔</span></span> | <span data-ttu-id="8f2a6-212">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-212">✔</span></span>
<span data-ttu-id="8f2a6-213">Geometry. 境界</span><span class="sxs-lookup"><span data-stu-id="8f2a6-213">Geometry.Boundary</span></span> | <span data-ttu-id="8f2a6-214">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-214">✔</span></span> | | <span data-ttu-id="8f2a6-215">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-215">✔</span></span> | <span data-ttu-id="8f2a6-216">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-216">✔</span></span>
<span data-ttu-id="8f2a6-217">Geometry. Buffer (double)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="8f2a6-218">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-218">✔</span></span> | <span data-ttu-id="8f2a6-219">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-219">✔</span></span> | <span data-ttu-id="8f2a6-220">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-220">✔</span></span> | <span data-ttu-id="8f2a6-221">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-221">✔</span></span>
<span data-ttu-id="8f2a6-222">Geometry. Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="8f2a6-223">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-223">✔</span></span> | <span data-ttu-id="8f2a6-224">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-224">✔</span></span>
<span data-ttu-id="8f2a6-225">Geometry. 重心</span><span class="sxs-lookup"><span data-stu-id="8f2a6-225">Geometry.Centroid</span></span> | <span data-ttu-id="8f2a6-226">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-226">✔</span></span> | | <span data-ttu-id="8f2a6-227">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-227">✔</span></span> | <span data-ttu-id="8f2a6-228">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-228">✔</span></span>
<span data-ttu-id="8f2a6-229">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="8f2a6-230">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-230">✔</span></span> | <span data-ttu-id="8f2a6-231">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-231">✔</span></span> | <span data-ttu-id="8f2a6-232">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-232">✔</span></span> | <span data-ttu-id="8f2a6-233">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-233">✔</span></span>
<span data-ttu-id="8f2a6-234">ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="8f2a6-235">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-235">✔</span></span> | <span data-ttu-id="8f2a6-236">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-236">✔</span></span> | <span data-ttu-id="8f2a6-237">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-237">✔</span></span> | <span data-ttu-id="8f2a6-238">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-238">✔</span></span>
<span data-ttu-id="8f2a6-239">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="8f2a6-240">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-240">✔</span></span> | <span data-ttu-id="8f2a6-241">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-241">✔</span></span>
<span data-ttu-id="8f2a6-242">Geometry. カバー (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="8f2a6-243">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-243">✔</span></span> | <span data-ttu-id="8f2a6-244">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-244">✔</span></span>
<span data-ttu-id="8f2a6-245">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="8f2a6-246">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-246">✔</span></span> | | <span data-ttu-id="8f2a6-247">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-247">✔</span></span> | <span data-ttu-id="8f2a6-248">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-248">✔</span></span>
<span data-ttu-id="8f2a6-249">Geometry. 差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="8f2a6-250">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-250">✔</span></span> | <span data-ttu-id="8f2a6-251">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-251">✔</span></span> | <span data-ttu-id="8f2a6-252">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-252">✔</span></span> | <span data-ttu-id="8f2a6-253">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-253">✔</span></span>
<span data-ttu-id="8f2a6-254">Geometry. ディメンション</span><span class="sxs-lookup"><span data-stu-id="8f2a6-254">Geometry.Dimension</span></span> | <span data-ttu-id="8f2a6-255">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-255">✔</span></span> | <span data-ttu-id="8f2a6-256">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-256">✔</span></span> | <span data-ttu-id="8f2a6-257">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-257">✔</span></span> | <span data-ttu-id="8f2a6-258">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-258">✔</span></span>
<span data-ttu-id="8f2a6-259">Geometry. 不整合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="8f2a6-260">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-260">✔</span></span> | <span data-ttu-id="8f2a6-261">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-261">✔</span></span> | <span data-ttu-id="8f2a6-262">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-262">✔</span></span> | <span data-ttu-id="8f2a6-263">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-263">✔</span></span>
<span data-ttu-id="8f2a6-264">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="8f2a6-265">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-265">✔</span></span> | <span data-ttu-id="8f2a6-266">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-266">✔</span></span> | <span data-ttu-id="8f2a6-267">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-267">✔</span></span> | <span data-ttu-id="8f2a6-268">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-268">✔</span></span>
<span data-ttu-id="8f2a6-269">Geometry. エンベロープ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-269">Geometry.Envelope</span></span> | <span data-ttu-id="8f2a6-270">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-270">✔</span></span> | | <span data-ttu-id="8f2a6-271">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-271">✔</span></span> | <span data-ttu-id="8f2a6-272">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-272">✔</span></span>
<span data-ttu-id="8f2a6-273">EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="8f2a6-274">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-274">✔</span></span>
<span data-ttu-id="8f2a6-275">EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="8f2a6-276">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-276">✔</span></span> | <span data-ttu-id="8f2a6-277">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-277">✔</span></span> | <span data-ttu-id="8f2a6-278">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-278">✔</span></span> | <span data-ttu-id="8f2a6-279">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-279">✔</span></span>
<span data-ttu-id="8f2a6-280">GeometryType</span><span class="sxs-lookup"><span data-stu-id="8f2a6-280">Geometry.GeometryType</span></span> | <span data-ttu-id="8f2a6-281">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-281">✔</span></span> | <span data-ttu-id="8f2a6-282">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-282">✔</span></span> | <span data-ttu-id="8f2a6-283">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-283">✔</span></span> | <span data-ttu-id="8f2a6-284">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-284">✔</span></span>
<span data-ttu-id="8f2a6-285">GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="8f2a6-286">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-286">✔</span></span> | | <span data-ttu-id="8f2a6-287">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-287">✔</span></span> | <span data-ttu-id="8f2a6-288">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-288">✔</span></span>
<span data-ttu-id="8f2a6-289">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="8f2a6-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="8f2a6-290">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-290">✔</span></span> | | <span data-ttu-id="8f2a6-291">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-291">✔</span></span> | <span data-ttu-id="8f2a6-292">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-292">✔</span></span>
<span data-ttu-id="8f2a6-293">Geometry. 積集合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-293">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="8f2a6-294">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-294">✔</span></span> | <span data-ttu-id="8f2a6-295">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-295">✔</span></span> | <span data-ttu-id="8f2a6-296">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-296">✔</span></span> | <span data-ttu-id="8f2a6-297">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-297">✔</span></span>
<span data-ttu-id="8f2a6-298">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-298">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="8f2a6-299">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-299">✔</span></span> | <span data-ttu-id="8f2a6-300">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-300">✔</span></span> | <span data-ttu-id="8f2a6-301">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-301">✔</span></span> | <span data-ttu-id="8f2a6-302">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-302">✔</span></span>
<span data-ttu-id="8f2a6-303">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="8f2a6-303">Geometry.IsEmpty</span></span> | <span data-ttu-id="8f2a6-304">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-304">✔</span></span> | <span data-ttu-id="8f2a6-305">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-305">✔</span></span> | <span data-ttu-id="8f2a6-306">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-306">✔</span></span> | <span data-ttu-id="8f2a6-307">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-307">✔</span></span>
<span data-ttu-id="8f2a6-308">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="8f2a6-308">Geometry.IsSimple</span></span> | <span data-ttu-id="8f2a6-309">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-309">✔</span></span> | | <span data-ttu-id="8f2a6-310">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-310">✔</span></span> | <span data-ttu-id="8f2a6-311">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-311">✔</span></span>
<span data-ttu-id="8f2a6-312">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="8f2a6-312">Geometry.IsValid</span></span> | <span data-ttu-id="8f2a6-313">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-313">✔</span></span> | <span data-ttu-id="8f2a6-314">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-314">✔</span></span> | <span data-ttu-id="8f2a6-315">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-315">✔</span></span> | <span data-ttu-id="8f2a6-316">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-316">✔</span></span>
<span data-ttu-id="8f2a6-317">Geometry. Iswithin Distance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-317">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="8f2a6-318">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-318">✔</span></span> | | <span data-ttu-id="8f2a6-319">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-319">✔</span></span> | <span data-ttu-id="8f2a6-320">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-320">✔</span></span>
<span data-ttu-id="8f2a6-321">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="8f2a6-321">Geometry.Length</span></span> | <span data-ttu-id="8f2a6-322">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-322">✔</span></span> | <span data-ttu-id="8f2a6-323">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-323">✔</span></span> | <span data-ttu-id="8f2a6-324">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-324">✔</span></span> | <span data-ttu-id="8f2a6-325">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-325">✔</span></span>
<span data-ttu-id="8f2a6-326">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="8f2a6-326">Geometry.NumGeometries</span></span> | <span data-ttu-id="8f2a6-327">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-327">✔</span></span> | <span data-ttu-id="8f2a6-328">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-328">✔</span></span> | <span data-ttu-id="8f2a6-329">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-329">✔</span></span> | <span data-ttu-id="8f2a6-330">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-330">✔</span></span>
<span data-ttu-id="8f2a6-331">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="8f2a6-331">Geometry.NumPoints</span></span> | <span data-ttu-id="8f2a6-332">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-332">✔</span></span> | <span data-ttu-id="8f2a6-333">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-333">✔</span></span> | <span data-ttu-id="8f2a6-334">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-334">✔</span></span> | <span data-ttu-id="8f2a6-335">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-335">✔</span></span>
<span data-ttu-id="8f2a6-336">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="8f2a6-336">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="8f2a6-337">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-337">✔</span></span> | <span data-ttu-id="8f2a6-338">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-338">✔</span></span> | <span data-ttu-id="8f2a6-339">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-339">✔</span></span> | <span data-ttu-id="8f2a6-340">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-340">✔</span></span>
<span data-ttu-id="8f2a6-341">Geometry. 重なり (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-341">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="8f2a6-342">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-342">✔</span></span> | <span data-ttu-id="8f2a6-343">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-343">✔</span></span> | <span data-ttu-id="8f2a6-344">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-344">✔</span></span> | <span data-ttu-id="8f2a6-345">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-345">✔</span></span>
<span data-ttu-id="8f2a6-346">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="8f2a6-346">Geometry.PointOnSurface</span></span> | <span data-ttu-id="8f2a6-347">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-347">✔</span></span> | | <span data-ttu-id="8f2a6-348">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-348">✔</span></span> | <span data-ttu-id="8f2a6-349">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-349">✔</span></span>
<span data-ttu-id="8f2a6-350">Geometry. 関連付け (Geometry, string)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-350">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="8f2a6-351">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-351">✔</span></span> | | <span data-ttu-id="8f2a6-352">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-352">✔</span></span> | <span data-ttu-id="8f2a6-353">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-353">✔</span></span>
<span data-ttu-id="8f2a6-354">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-354">Geometry.Reverse()</span></span> | | | <span data-ttu-id="8f2a6-355">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-355">✔</span></span> | <span data-ttu-id="8f2a6-356">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-356">✔</span></span>
<span data-ttu-id="8f2a6-357">SRID</span><span class="sxs-lookup"><span data-stu-id="8f2a6-357">Geometry.SRID</span></span> | <span data-ttu-id="8f2a6-358">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-358">✔</span></span> | <span data-ttu-id="8f2a6-359">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-359">✔</span></span> | <span data-ttu-id="8f2a6-360">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-360">✔</span></span> | <span data-ttu-id="8f2a6-361">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-361">✔</span></span>
<span data-ttu-id="8f2a6-362">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-362">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="8f2a6-363">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-363">✔</span></span> | <span data-ttu-id="8f2a6-364">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-364">✔</span></span> | <span data-ttu-id="8f2a6-365">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-365">✔</span></span> | <span data-ttu-id="8f2a6-366">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-366">✔</span></span>
<span data-ttu-id="8f2a6-367">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-367">Geometry.ToBinary()</span></span> | <span data-ttu-id="8f2a6-368">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-368">✔</span></span> | <span data-ttu-id="8f2a6-369">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-369">✔</span></span> | <span data-ttu-id="8f2a6-370">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-370">✔</span></span> | <span data-ttu-id="8f2a6-371">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-371">✔</span></span>
<span data-ttu-id="8f2a6-372">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-372">Geometry.ToText()</span></span> | <span data-ttu-id="8f2a6-373">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-373">✔</span></span> | <span data-ttu-id="8f2a6-374">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-374">✔</span></span> | <span data-ttu-id="8f2a6-375">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-375">✔</span></span> | <span data-ttu-id="8f2a6-376">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-376">✔</span></span>
<span data-ttu-id="8f2a6-377">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-377">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="8f2a6-378">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-378">✔</span></span> | | <span data-ttu-id="8f2a6-379">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-379">✔</span></span> | <span data-ttu-id="8f2a6-380">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-380">✔</span></span>
<span data-ttu-id="8f2a6-381">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="8f2a6-381">Geometry.Union()</span></span> | | | <span data-ttu-id="8f2a6-382">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-382">✔</span></span> | <span data-ttu-id="8f2a6-383">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-383">✔</span></span>
<span data-ttu-id="8f2a6-384">Geometry. 共用体 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-384">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="8f2a6-385">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-385">✔</span></span> | <span data-ttu-id="8f2a6-386">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-386">✔</span></span> | <span data-ttu-id="8f2a6-387">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-387">✔</span></span> | <span data-ttu-id="8f2a6-388">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-388">✔</span></span>
<span data-ttu-id="8f2a6-389">Geometry. 内 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-389">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="8f2a6-390">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-390">✔</span></span> | <span data-ttu-id="8f2a6-391">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-391">✔</span></span> | <span data-ttu-id="8f2a6-392">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-392">✔</span></span> | <span data-ttu-id="8f2a6-393">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-393">✔</span></span>
<span data-ttu-id="8f2a6-394">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="8f2a6-394">GeometryCollection.Count</span></span> | <span data-ttu-id="8f2a6-395">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-395">✔</span></span> | <span data-ttu-id="8f2a6-396">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-396">✔</span></span> | <span data-ttu-id="8f2a6-397">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-397">✔</span></span> | <span data-ttu-id="8f2a6-398">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-398">✔</span></span>
<span data-ttu-id="8f2a6-399">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="8f2a6-399">GeometryCollection[int]</span></span> | <span data-ttu-id="8f2a6-400">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-400">✔</span></span> | <span data-ttu-id="8f2a6-401">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-401">✔</span></span> | <span data-ttu-id="8f2a6-402">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-402">✔</span></span> | <span data-ttu-id="8f2a6-403">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-403">✔</span></span>
<span data-ttu-id="8f2a6-404">LineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-404">LineString.Count</span></span> | <span data-ttu-id="8f2a6-405">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-405">✔</span></span> | <span data-ttu-id="8f2a6-406">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-406">✔</span></span> | <span data-ttu-id="8f2a6-407">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-407">✔</span></span> | <span data-ttu-id="8f2a6-408">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-408">✔</span></span>
<span data-ttu-id="8f2a6-409">LineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-409">LineString.EndPoint</span></span> | <span data-ttu-id="8f2a6-410">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-410">✔</span></span> | <span data-ttu-id="8f2a6-411">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-411">✔</span></span> | <span data-ttu-id="8f2a6-412">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-412">✔</span></span> | <span data-ttu-id="8f2a6-413">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-413">✔</span></span>
<span data-ttu-id="8f2a6-414">LineString n (int)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-414">LineString.GetPointN(int)</span></span> | <span data-ttu-id="8f2a6-415">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-415">✔</span></span> | <span data-ttu-id="8f2a6-416">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-416">✔</span></span> | <span data-ttu-id="8f2a6-417">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-417">✔</span></span> | <span data-ttu-id="8f2a6-418">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-418">✔</span></span>
<span data-ttu-id="8f2a6-419">LineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-419">LineString.IsClosed</span></span> | <span data-ttu-id="8f2a6-420">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-420">✔</span></span> | <span data-ttu-id="8f2a6-421">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-421">✔</span></span> | <span data-ttu-id="8f2a6-422">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-422">✔</span></span> | <span data-ttu-id="8f2a6-423">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-423">✔</span></span>
<span data-ttu-id="8f2a6-424">LineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-424">LineString.IsRing</span></span> | <span data-ttu-id="8f2a6-425">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-425">✔</span></span> | | <span data-ttu-id="8f2a6-426">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-426">✔</span></span> | <span data-ttu-id="8f2a6-427">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-427">✔</span></span>
<span data-ttu-id="8f2a6-428">LineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="8f2a6-428">LineString.StartPoint</span></span> | <span data-ttu-id="8f2a6-429">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-429">✔</span></span> | <span data-ttu-id="8f2a6-430">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-430">✔</span></span> | <span data-ttu-id="8f2a6-431">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-431">✔</span></span> | <span data-ttu-id="8f2a6-432">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-432">✔</span></span>
<span data-ttu-id="8f2a6-433">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="8f2a6-433">MultiLineString.IsClosed</span></span> | <span data-ttu-id="8f2a6-434">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-434">✔</span></span> | <span data-ttu-id="8f2a6-435">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-435">✔</span></span> | <span data-ttu-id="8f2a6-436">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-436">✔</span></span> | <span data-ttu-id="8f2a6-437">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-437">✔</span></span>
<span data-ttu-id="8f2a6-438">Point. M</span><span class="sxs-lookup"><span data-stu-id="8f2a6-438">Point.M</span></span> | <span data-ttu-id="8f2a6-439">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-439">✔</span></span> | <span data-ttu-id="8f2a6-440">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-440">✔</span></span> | <span data-ttu-id="8f2a6-441">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-441">✔</span></span> | <span data-ttu-id="8f2a6-442">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-442">✔</span></span>
<span data-ttu-id="8f2a6-443">Point. X</span><span class="sxs-lookup"><span data-stu-id="8f2a6-443">Point.X</span></span> | <span data-ttu-id="8f2a6-444">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-444">✔</span></span> | <span data-ttu-id="8f2a6-445">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-445">✔</span></span> | <span data-ttu-id="8f2a6-446">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-446">✔</span></span> | <span data-ttu-id="8f2a6-447">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-447">✔</span></span>
<span data-ttu-id="8f2a6-448">Point. Y</span><span class="sxs-lookup"><span data-stu-id="8f2a6-448">Point.Y</span></span> | <span data-ttu-id="8f2a6-449">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-449">✔</span></span> | <span data-ttu-id="8f2a6-450">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-450">✔</span></span> | <span data-ttu-id="8f2a6-451">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-451">✔</span></span> | <span data-ttu-id="8f2a6-452">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-452">✔</span></span>
<span data-ttu-id="8f2a6-453">Point. Z</span><span class="sxs-lookup"><span data-stu-id="8f2a6-453">Point.Z</span></span> | <span data-ttu-id="8f2a6-454">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-454">✔</span></span> | <span data-ttu-id="8f2a6-455">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-455">✔</span></span> | <span data-ttu-id="8f2a6-456">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-456">✔</span></span> | <span data-ttu-id="8f2a6-457">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-457">✔</span></span>
<span data-ttu-id="8f2a6-458">ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="8f2a6-458">Polygon.ExteriorRing</span></span> | <span data-ttu-id="8f2a6-459">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-459">✔</span></span> | <span data-ttu-id="8f2a6-460">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-460">✔</span></span> | <span data-ttu-id="8f2a6-461">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-461">✔</span></span> | <span data-ttu-id="8f2a6-462">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-462">✔</span></span>
<span data-ttu-id="8f2a6-463">GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="8f2a6-463">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="8f2a6-464">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-464">✔</span></span> | <span data-ttu-id="8f2a6-465">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-465">✔</span></span> | <span data-ttu-id="8f2a6-466">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-466">✔</span></span> | <span data-ttu-id="8f2a6-467">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-467">✔</span></span>
<span data-ttu-id="8f2a6-468">NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="8f2a6-468">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="8f2a6-469">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-469">✔</span></span> | <span data-ttu-id="8f2a6-470">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-470">✔</span></span> | <span data-ttu-id="8f2a6-471">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-471">✔</span></span> | <span data-ttu-id="8f2a6-472">✔</span><span class="sxs-lookup"><span data-stu-id="8f2a6-472">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f2a6-473">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8f2a6-473">Additional resources</span></span>

* [<span data-ttu-id="8f2a6-474">SQL Server の空間データ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-474">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="8f2a6-475">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="8f2a6-475">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="8f2a6-476">Npgsql 空間のドキュメント</span><span class="sxs-lookup"><span data-stu-id="8f2a6-476">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="8f2a6-477">PostGIS のドキュメント</span><span class="sxs-lookup"><span data-stu-id="8f2a6-477">PostGIS Documentation</span></span>](https://postgis.net/documentation/)

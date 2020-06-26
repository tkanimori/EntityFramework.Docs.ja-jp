---
title: 空間データ-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 2222df84be7bfde3f252766bef1cfab39b476efa
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370449"
---
# <a name="spatial-data"></a><span data-ttu-id="4fe6d-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="4fe6d-103">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="4fe6d-104">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="4fe6d-105">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="4fe6d-106">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="4fe6d-107">EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="4fe6d-108">インストール</span><span class="sxs-lookup"><span data-stu-id="4fe6d-108">Installing</span></span>

<span data-ttu-id="4fe6d-109">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="4fe6d-110">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="4fe6d-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4fe6d-111">EF Core Provider</span></span>                        | <span data-ttu-id="4fe6d-112">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="4fe6d-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4fe6d-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="4fe6d-114">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="4fe6d-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="4fe6d-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="4fe6d-116">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="4fe6d-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="4fe6d-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="4fe6d-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4fe6d-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="4fe6d-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="4fe6d-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="4fe6d-120">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4fe6d-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="4fe6d-121">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="4fe6d-121">Reverse engineering</span></span>

<span data-ttu-id="4fe6d-122">空間 NuGet パッケージでは、空間プロパティを使用してモデルの[リバースエンジニアリング](../managing-schemas/scaffolding.md)を行うこともできますが、またはを実行する***前***にパッケージをインストールする必要があり `Scaffold-DbContext` `dotnet ef dbcontext scaffold` ます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="4fe6d-123">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="4fe6d-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="4fe6d-125">NetTopologySuite は、.NET 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="4fe6d-126">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="4fe6d-127">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="4fe6d-128">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="4fe6d-129">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-129">There are several spatial data types.</span></span> <span data-ttu-id="4fe6d-130">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="4fe6d-131">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="4fe6d-132">これらは名前空間内にあり `NetTopologySuite.Geometries` ます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="4fe6d-133">ジオメトリ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-133">Geometry</span></span>
  * <span data-ttu-id="4fe6d-134">ポイント</span><span class="sxs-lookup"><span data-stu-id="4fe6d-134">Point</span></span>
  * <span data-ttu-id="4fe6d-135">LineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-135">LineString</span></span>
  * <span data-ttu-id="4fe6d-136">多角形</span><span class="sxs-lookup"><span data-stu-id="4fe6d-136">Polygon</span></span>
  * <span data-ttu-id="4fe6d-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="4fe6d-137">GeometryCollection</span></span>
    * <span data-ttu-id="4fe6d-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="4fe6d-138">MultiPoint</span></span>
    * <span data-ttu-id="4fe6d-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-139">MultiLineString</span></span>
    * <span data-ttu-id="4fe6d-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="4fe6d-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="4fe6d-141">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="4fe6d-142">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="4fe6d-143">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="4fe6d-144">値の作成</span><span class="sxs-lookup"><span data-stu-id="4fe6d-144">Creating values</span></span>

<span data-ttu-id="4fe6d-145">コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="4fe6d-146">これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用される) や座標シーケンス (使用可能な次元とメジャーを決定) など、より高度なものを制御できます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="4fe6d-147">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="4fe6d-148">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="4fe6d-148">Longitude and Latitude</span></span>

<span data-ttu-id="4fe6d-149">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="4fe6d-150">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="4fe6d-151">これは、通常、これらの値が表示されるのとは**逆**の形式であることに注意して `latitude, longitude` ください。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="4fe6d-152">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="4fe6d-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="4fe6d-153">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="4fe6d-154">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="4fe6d-155">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="4fe6d-156">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="4fe6d-157">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="4fe6d-158">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="4fe6d-159">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-159">Querying Data</span></span>

<span data-ttu-id="4fe6d-160">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="4fe6d-161">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="4fe6d-162">この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="4fe6d-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4fe6d-163">SQL Server</span></span>

<span data-ttu-id="4fe6d-164">SQL Server を使用している場合は、注意が必要な点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="4fe6d-165">Geography または geometry</span><span class="sxs-lookup"><span data-stu-id="4fe6d-165">Geography or geometry</span></span>

<span data-ttu-id="4fe6d-166">既定では、空間プロパティは SQL Server の列にマップされ `geography` ます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="4fe6d-167">を使用するには `geometry` 、モデルの[列の型を構成](xref:core/modeling/entity-properties#column-data-types)します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="4fe6d-168">Geography polygon リング</span><span class="sxs-lookup"><span data-stu-id="4fe6d-168">Geography polygon rings</span></span>

<span data-ttu-id="4fe6d-169">列の型を使用する場合、SQL Server によって `geography` 、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="4fe6d-170">外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="4fe6d-171">NTS は、データベースに値を送信する前にこのことを検証します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="4fe6d-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="4fe6d-172">FullGlobe</span></span>

<span data-ttu-id="4fe6d-173">SQL Server には、列の型を使用するときに全地球を表す非標準の geometry 型があり `geography` ます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="4fe6d-174">また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="4fe6d-175">これらのいずれも、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="4fe6d-176">NTS では、これに基づく FullGlobe と多角形はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="4fe6d-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="4fe6d-177">SQLite</span></span>

<span data-ttu-id="4fe6d-178">SQLite を使用する場合の追加情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="4fe6d-179">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="4fe6d-179">Installing SpatiaLite</span></span>

<span data-ttu-id="4fe6d-180">Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="4fe6d-181">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="4fe6d-182">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="4fe6d-183">たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="4fe6d-184">残念ながら、新しいバージョンの PROJ (SpatiaLite の依存関係) は EF の既定の[SQLitePCLRaw バンドル](/dotnet/standard/data/sqlite/custom-versions#bundles)と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="4fe6d-185">この問題を回避するには、システム SQLite ライブラリを使用するカスタム[SQLitePCLRaw プロバイダー](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers)を作成するか、または SpatiaLite のカスタムビルドをインストールして、PROJ サポートを無効にします。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a><span data-ttu-id="4fe6d-186">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="4fe6d-186">Configuring SRID</span></span>

<span data-ttu-id="4fe6d-187">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="4fe6d-188">既定の SRID は `0` です。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-188">The default SRID is `0`.</span></span> <span data-ttu-id="4fe6d-189">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="4fe6d-190">Dimension</span><span class="sxs-lookup"><span data-stu-id="4fe6d-190">Dimension</span></span>

<span data-ttu-id="4fe6d-191">SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="4fe6d-192">既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="4fe6d-193">翻訳された操作</span><span class="sxs-lookup"><span data-stu-id="4fe6d-193">Translated Operations</span></span>

<span data-ttu-id="4fe6d-194">次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="4fe6d-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="4fe6d-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4fe6d-195">NetTopologySuite</span></span> | <span data-ttu-id="4fe6d-196">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-196">SQL Server (geometry)</span></span> | <span data-ttu-id="4fe6d-197">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-197">SQL Server (geography)</span></span> | <span data-ttu-id="4fe6d-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="4fe6d-198">SQLite</span></span> | <span data-ttu-id="4fe6d-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="4fe6d-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="4fe6d-200">Geometry. 面</span><span class="sxs-lookup"><span data-stu-id="4fe6d-200">Geometry.Area</span></span> | <span data-ttu-id="4fe6d-201">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-201">✔</span></span> | <span data-ttu-id="4fe6d-202">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-202">✔</span></span> | <span data-ttu-id="4fe6d-203">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-203">✔</span></span> | <span data-ttu-id="4fe6d-204">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-204">✔</span></span>
<span data-ttu-id="4fe6d-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="4fe6d-206">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-206">✔</span></span> | <span data-ttu-id="4fe6d-207">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-207">✔</span></span> | <span data-ttu-id="4fe6d-208">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-208">✔</span></span> | <span data-ttu-id="4fe6d-209">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-209">✔</span></span>
<span data-ttu-id="4fe6d-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-210">Geometry.AsText()</span></span> | <span data-ttu-id="4fe6d-211">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-211">✔</span></span> | <span data-ttu-id="4fe6d-212">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-212">✔</span></span> | <span data-ttu-id="4fe6d-213">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-213">✔</span></span> | <span data-ttu-id="4fe6d-214">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-214">✔</span></span>
<span data-ttu-id="4fe6d-215">Geometry. 境界</span><span class="sxs-lookup"><span data-stu-id="4fe6d-215">Geometry.Boundary</span></span> | <span data-ttu-id="4fe6d-216">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-216">✔</span></span> | | <span data-ttu-id="4fe6d-217">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-217">✔</span></span> | <span data-ttu-id="4fe6d-218">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-218">✔</span></span>
<span data-ttu-id="4fe6d-219">Geometry. Buffer (double)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="4fe6d-220">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-220">✔</span></span> | <span data-ttu-id="4fe6d-221">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-221">✔</span></span> | <span data-ttu-id="4fe6d-222">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-222">✔</span></span> | <span data-ttu-id="4fe6d-223">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-223">✔</span></span>
<span data-ttu-id="4fe6d-224">Geometry. Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="4fe6d-225">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-225">✔</span></span> | <span data-ttu-id="4fe6d-226">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-226">✔</span></span>
<span data-ttu-id="4fe6d-227">Geometry. 重心</span><span class="sxs-lookup"><span data-stu-id="4fe6d-227">Geometry.Centroid</span></span> | <span data-ttu-id="4fe6d-228">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-228">✔</span></span> | | <span data-ttu-id="4fe6d-229">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-229">✔</span></span> | <span data-ttu-id="4fe6d-230">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-230">✔</span></span>
<span data-ttu-id="4fe6d-231">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="4fe6d-232">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-232">✔</span></span> | <span data-ttu-id="4fe6d-233">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-233">✔</span></span> | <span data-ttu-id="4fe6d-234">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-234">✔</span></span> | <span data-ttu-id="4fe6d-235">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-235">✔</span></span>
<span data-ttu-id="4fe6d-236">ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="4fe6d-237">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-237">✔</span></span> | <span data-ttu-id="4fe6d-238">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-238">✔</span></span> | <span data-ttu-id="4fe6d-239">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-239">✔</span></span> | <span data-ttu-id="4fe6d-240">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-240">✔</span></span>
<span data-ttu-id="4fe6d-241">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="4fe6d-242">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-242">✔</span></span> | <span data-ttu-id="4fe6d-243">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-243">✔</span></span>
<span data-ttu-id="4fe6d-244">Geometry. カバー (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="4fe6d-245">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-245">✔</span></span> | <span data-ttu-id="4fe6d-246">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-246">✔</span></span>
<span data-ttu-id="4fe6d-247">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="4fe6d-248">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-248">✔</span></span> | | <span data-ttu-id="4fe6d-249">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-249">✔</span></span> | <span data-ttu-id="4fe6d-250">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-250">✔</span></span>
<span data-ttu-id="4fe6d-251">Geometry. 差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="4fe6d-252">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-252">✔</span></span> | <span data-ttu-id="4fe6d-253">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-253">✔</span></span> | <span data-ttu-id="4fe6d-254">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-254">✔</span></span> | <span data-ttu-id="4fe6d-255">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-255">✔</span></span>
<span data-ttu-id="4fe6d-256">Geometry. ディメンション</span><span class="sxs-lookup"><span data-stu-id="4fe6d-256">Geometry.Dimension</span></span> | <span data-ttu-id="4fe6d-257">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-257">✔</span></span> | <span data-ttu-id="4fe6d-258">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-258">✔</span></span> | <span data-ttu-id="4fe6d-259">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-259">✔</span></span> | <span data-ttu-id="4fe6d-260">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-260">✔</span></span>
<span data-ttu-id="4fe6d-261">Geometry. 不整合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="4fe6d-262">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-262">✔</span></span> | <span data-ttu-id="4fe6d-263">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-263">✔</span></span> | <span data-ttu-id="4fe6d-264">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-264">✔</span></span> | <span data-ttu-id="4fe6d-265">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-265">✔</span></span>
<span data-ttu-id="4fe6d-266">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="4fe6d-267">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-267">✔</span></span> | <span data-ttu-id="4fe6d-268">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-268">✔</span></span> | <span data-ttu-id="4fe6d-269">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-269">✔</span></span> | <span data-ttu-id="4fe6d-270">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-270">✔</span></span>
<span data-ttu-id="4fe6d-271">Geometry. エンベロープ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-271">Geometry.Envelope</span></span> | <span data-ttu-id="4fe6d-272">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-272">✔</span></span> | | <span data-ttu-id="4fe6d-273">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-273">✔</span></span> | <span data-ttu-id="4fe6d-274">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-274">✔</span></span>
<span data-ttu-id="4fe6d-275">EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="4fe6d-276">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-276">✔</span></span>
<span data-ttu-id="4fe6d-277">EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="4fe6d-278">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-278">✔</span></span> | <span data-ttu-id="4fe6d-279">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-279">✔</span></span> | <span data-ttu-id="4fe6d-280">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-280">✔</span></span> | <span data-ttu-id="4fe6d-281">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-281">✔</span></span>
<span data-ttu-id="4fe6d-282">GeometryType</span><span class="sxs-lookup"><span data-stu-id="4fe6d-282">Geometry.GeometryType</span></span> | <span data-ttu-id="4fe6d-283">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-283">✔</span></span> | <span data-ttu-id="4fe6d-284">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-284">✔</span></span> | <span data-ttu-id="4fe6d-285">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-285">✔</span></span> | <span data-ttu-id="4fe6d-286">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-286">✔</span></span>
<span data-ttu-id="4fe6d-287">GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="4fe6d-288">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-288">✔</span></span> | | <span data-ttu-id="4fe6d-289">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-289">✔</span></span> | <span data-ttu-id="4fe6d-290">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-290">✔</span></span>
<span data-ttu-id="4fe6d-291">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="4fe6d-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="4fe6d-292">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-292">✔</span></span> | | <span data-ttu-id="4fe6d-293">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-293">✔</span></span> | <span data-ttu-id="4fe6d-294">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-294">✔</span></span>
<span data-ttu-id="4fe6d-295">Geometry. 積集合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="4fe6d-296">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-296">✔</span></span> | <span data-ttu-id="4fe6d-297">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-297">✔</span></span> | <span data-ttu-id="4fe6d-298">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-298">✔</span></span> | <span data-ttu-id="4fe6d-299">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-299">✔</span></span>
<span data-ttu-id="4fe6d-300">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="4fe6d-301">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-301">✔</span></span> | <span data-ttu-id="4fe6d-302">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-302">✔</span></span> | <span data-ttu-id="4fe6d-303">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-303">✔</span></span> | <span data-ttu-id="4fe6d-304">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-304">✔</span></span>
<span data-ttu-id="4fe6d-305">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="4fe6d-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="4fe6d-306">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-306">✔</span></span> | <span data-ttu-id="4fe6d-307">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-307">✔</span></span> | <span data-ttu-id="4fe6d-308">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-308">✔</span></span> | <span data-ttu-id="4fe6d-309">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-309">✔</span></span>
<span data-ttu-id="4fe6d-310">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="4fe6d-310">Geometry.IsSimple</span></span> | <span data-ttu-id="4fe6d-311">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-311">✔</span></span> | | <span data-ttu-id="4fe6d-312">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-312">✔</span></span> | <span data-ttu-id="4fe6d-313">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-313">✔</span></span>
<span data-ttu-id="4fe6d-314">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="4fe6d-314">Geometry.IsValid</span></span> | <span data-ttu-id="4fe6d-315">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-315">✔</span></span> | <span data-ttu-id="4fe6d-316">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-316">✔</span></span> | <span data-ttu-id="4fe6d-317">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-317">✔</span></span> | <span data-ttu-id="4fe6d-318">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-318">✔</span></span>
<span data-ttu-id="4fe6d-319">Geometry. Iswithin Distance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="4fe6d-320">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-320">✔</span></span> | | <span data-ttu-id="4fe6d-321">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-321">✔</span></span> | <span data-ttu-id="4fe6d-322">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-322">✔</span></span>
<span data-ttu-id="4fe6d-323">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="4fe6d-323">Geometry.Length</span></span> | <span data-ttu-id="4fe6d-324">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-324">✔</span></span> | <span data-ttu-id="4fe6d-325">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-325">✔</span></span> | <span data-ttu-id="4fe6d-326">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-326">✔</span></span> | <span data-ttu-id="4fe6d-327">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-327">✔</span></span>
<span data-ttu-id="4fe6d-328">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="4fe6d-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="4fe6d-329">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-329">✔</span></span> | <span data-ttu-id="4fe6d-330">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-330">✔</span></span> | <span data-ttu-id="4fe6d-331">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-331">✔</span></span> | <span data-ttu-id="4fe6d-332">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-332">✔</span></span>
<span data-ttu-id="4fe6d-333">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="4fe6d-333">Geometry.NumPoints</span></span> | <span data-ttu-id="4fe6d-334">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-334">✔</span></span> | <span data-ttu-id="4fe6d-335">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-335">✔</span></span> | <span data-ttu-id="4fe6d-336">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-336">✔</span></span> | <span data-ttu-id="4fe6d-337">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-337">✔</span></span>
<span data-ttu-id="4fe6d-338">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="4fe6d-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="4fe6d-339">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-339">✔</span></span> | <span data-ttu-id="4fe6d-340">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-340">✔</span></span> | <span data-ttu-id="4fe6d-341">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-341">✔</span></span> | <span data-ttu-id="4fe6d-342">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-342">✔</span></span>
<span data-ttu-id="4fe6d-343">Geometry. 重なり (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="4fe6d-344">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-344">✔</span></span> | <span data-ttu-id="4fe6d-345">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-345">✔</span></span> | <span data-ttu-id="4fe6d-346">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-346">✔</span></span> | <span data-ttu-id="4fe6d-347">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-347">✔</span></span>
<span data-ttu-id="4fe6d-348">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="4fe6d-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="4fe6d-349">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-349">✔</span></span> | | <span data-ttu-id="4fe6d-350">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-350">✔</span></span> | <span data-ttu-id="4fe6d-351">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-351">✔</span></span>
<span data-ttu-id="4fe6d-352">Geometry. 関連付け (Geometry, string)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="4fe6d-353">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-353">✔</span></span> | | <span data-ttu-id="4fe6d-354">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-354">✔</span></span> | <span data-ttu-id="4fe6d-355">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-355">✔</span></span>
<span data-ttu-id="4fe6d-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="4fe6d-357">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-357">✔</span></span> | <span data-ttu-id="4fe6d-358">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-358">✔</span></span>
<span data-ttu-id="4fe6d-359">SRID</span><span class="sxs-lookup"><span data-stu-id="4fe6d-359">Geometry.SRID</span></span> | <span data-ttu-id="4fe6d-360">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-360">✔</span></span> | <span data-ttu-id="4fe6d-361">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-361">✔</span></span> | <span data-ttu-id="4fe6d-362">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-362">✔</span></span> | <span data-ttu-id="4fe6d-363">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-363">✔</span></span>
<span data-ttu-id="4fe6d-364">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="4fe6d-365">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-365">✔</span></span> | <span data-ttu-id="4fe6d-366">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-366">✔</span></span> | <span data-ttu-id="4fe6d-367">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-367">✔</span></span> | <span data-ttu-id="4fe6d-368">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-368">✔</span></span>
<span data-ttu-id="4fe6d-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="4fe6d-370">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-370">✔</span></span> | <span data-ttu-id="4fe6d-371">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-371">✔</span></span> | <span data-ttu-id="4fe6d-372">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-372">✔</span></span> | <span data-ttu-id="4fe6d-373">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-373">✔</span></span>
<span data-ttu-id="4fe6d-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-374">Geometry.ToText()</span></span> | <span data-ttu-id="4fe6d-375">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-375">✔</span></span> | <span data-ttu-id="4fe6d-376">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-376">✔</span></span> | <span data-ttu-id="4fe6d-377">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-377">✔</span></span> | <span data-ttu-id="4fe6d-378">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-378">✔</span></span>
<span data-ttu-id="4fe6d-379">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="4fe6d-380">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-380">✔</span></span> | | <span data-ttu-id="4fe6d-381">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-381">✔</span></span> | <span data-ttu-id="4fe6d-382">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-382">✔</span></span>
<span data-ttu-id="4fe6d-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="4fe6d-383">Geometry.Union()</span></span> | | | <span data-ttu-id="4fe6d-384">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-384">✔</span></span> | <span data-ttu-id="4fe6d-385">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-385">✔</span></span>
<span data-ttu-id="4fe6d-386">Geometry. 共用体 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="4fe6d-387">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-387">✔</span></span> | <span data-ttu-id="4fe6d-388">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-388">✔</span></span> | <span data-ttu-id="4fe6d-389">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-389">✔</span></span> | <span data-ttu-id="4fe6d-390">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-390">✔</span></span>
<span data-ttu-id="4fe6d-391">Geometry. 内 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="4fe6d-392">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-392">✔</span></span> | <span data-ttu-id="4fe6d-393">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-393">✔</span></span> | <span data-ttu-id="4fe6d-394">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-394">✔</span></span> | <span data-ttu-id="4fe6d-395">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-395">✔</span></span>
<span data-ttu-id="4fe6d-396">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="4fe6d-396">GeometryCollection.Count</span></span> | <span data-ttu-id="4fe6d-397">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-397">✔</span></span> | <span data-ttu-id="4fe6d-398">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-398">✔</span></span> | <span data-ttu-id="4fe6d-399">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-399">✔</span></span> | <span data-ttu-id="4fe6d-400">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-400">✔</span></span>
<span data-ttu-id="4fe6d-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="4fe6d-401">GeometryCollection[int]</span></span> | <span data-ttu-id="4fe6d-402">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-402">✔</span></span> | <span data-ttu-id="4fe6d-403">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-403">✔</span></span> | <span data-ttu-id="4fe6d-404">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-404">✔</span></span> | <span data-ttu-id="4fe6d-405">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-405">✔</span></span>
<span data-ttu-id="4fe6d-406">LineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-406">LineString.Count</span></span> | <span data-ttu-id="4fe6d-407">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-407">✔</span></span> | <span data-ttu-id="4fe6d-408">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-408">✔</span></span> | <span data-ttu-id="4fe6d-409">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-409">✔</span></span> | <span data-ttu-id="4fe6d-410">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-410">✔</span></span>
<span data-ttu-id="4fe6d-411">LineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-411">LineString.EndPoint</span></span> | <span data-ttu-id="4fe6d-412">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-412">✔</span></span> | <span data-ttu-id="4fe6d-413">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-413">✔</span></span> | <span data-ttu-id="4fe6d-414">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-414">✔</span></span> | <span data-ttu-id="4fe6d-415">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-415">✔</span></span>
<span data-ttu-id="4fe6d-416">LineString n (int)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="4fe6d-417">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-417">✔</span></span> | <span data-ttu-id="4fe6d-418">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-418">✔</span></span> | <span data-ttu-id="4fe6d-419">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-419">✔</span></span> | <span data-ttu-id="4fe6d-420">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-420">✔</span></span>
<span data-ttu-id="4fe6d-421">LineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-421">LineString.IsClosed</span></span> | <span data-ttu-id="4fe6d-422">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-422">✔</span></span> | <span data-ttu-id="4fe6d-423">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-423">✔</span></span> | <span data-ttu-id="4fe6d-424">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-424">✔</span></span> | <span data-ttu-id="4fe6d-425">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-425">✔</span></span>
<span data-ttu-id="4fe6d-426">LineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-426">LineString.IsRing</span></span> | <span data-ttu-id="4fe6d-427">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-427">✔</span></span> | | <span data-ttu-id="4fe6d-428">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-428">✔</span></span> | <span data-ttu-id="4fe6d-429">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-429">✔</span></span>
<span data-ttu-id="4fe6d-430">LineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="4fe6d-430">LineString.StartPoint</span></span> | <span data-ttu-id="4fe6d-431">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-431">✔</span></span> | <span data-ttu-id="4fe6d-432">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-432">✔</span></span> | <span data-ttu-id="4fe6d-433">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-433">✔</span></span> | <span data-ttu-id="4fe6d-434">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-434">✔</span></span>
<span data-ttu-id="4fe6d-435">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="4fe6d-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="4fe6d-436">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-436">✔</span></span> | <span data-ttu-id="4fe6d-437">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-437">✔</span></span> | <span data-ttu-id="4fe6d-438">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-438">✔</span></span> | <span data-ttu-id="4fe6d-439">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-439">✔</span></span>
<span data-ttu-id="4fe6d-440">Point. M</span><span class="sxs-lookup"><span data-stu-id="4fe6d-440">Point.M</span></span> | <span data-ttu-id="4fe6d-441">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-441">✔</span></span> | <span data-ttu-id="4fe6d-442">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-442">✔</span></span> | <span data-ttu-id="4fe6d-443">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-443">✔</span></span> | <span data-ttu-id="4fe6d-444">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-444">✔</span></span>
<span data-ttu-id="4fe6d-445">Point. X</span><span class="sxs-lookup"><span data-stu-id="4fe6d-445">Point.X</span></span> | <span data-ttu-id="4fe6d-446">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-446">✔</span></span> | <span data-ttu-id="4fe6d-447">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-447">✔</span></span> | <span data-ttu-id="4fe6d-448">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-448">✔</span></span> | <span data-ttu-id="4fe6d-449">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-449">✔</span></span>
<span data-ttu-id="4fe6d-450">Point. Y</span><span class="sxs-lookup"><span data-stu-id="4fe6d-450">Point.Y</span></span> | <span data-ttu-id="4fe6d-451">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-451">✔</span></span> | <span data-ttu-id="4fe6d-452">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-452">✔</span></span> | <span data-ttu-id="4fe6d-453">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-453">✔</span></span> | <span data-ttu-id="4fe6d-454">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-454">✔</span></span>
<span data-ttu-id="4fe6d-455">Point. Z</span><span class="sxs-lookup"><span data-stu-id="4fe6d-455">Point.Z</span></span> | <span data-ttu-id="4fe6d-456">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-456">✔</span></span> | <span data-ttu-id="4fe6d-457">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-457">✔</span></span> | <span data-ttu-id="4fe6d-458">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-458">✔</span></span> | <span data-ttu-id="4fe6d-459">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-459">✔</span></span>
<span data-ttu-id="4fe6d-460">ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="4fe6d-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="4fe6d-461">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-461">✔</span></span> | <span data-ttu-id="4fe6d-462">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-462">✔</span></span> | <span data-ttu-id="4fe6d-463">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-463">✔</span></span> | <span data-ttu-id="4fe6d-464">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-464">✔</span></span>
<span data-ttu-id="4fe6d-465">GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="4fe6d-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="4fe6d-466">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-466">✔</span></span> | <span data-ttu-id="4fe6d-467">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-467">✔</span></span> | <span data-ttu-id="4fe6d-468">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-468">✔</span></span> | <span data-ttu-id="4fe6d-469">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-469">✔</span></span>
<span data-ttu-id="4fe6d-470">NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="4fe6d-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="4fe6d-471">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-471">✔</span></span> | <span data-ttu-id="4fe6d-472">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-472">✔</span></span> | <span data-ttu-id="4fe6d-473">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-473">✔</span></span> | <span data-ttu-id="4fe6d-474">✔</span><span class="sxs-lookup"><span data-stu-id="4fe6d-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4fe6d-475">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="4fe6d-475">Additional resources</span></span>

* [<span data-ttu-id="4fe6d-476">SQL Server の空間データ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-476">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="4fe6d-477">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="4fe6d-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="4fe6d-478">Npgsql 空間のドキュメント</span><span class="sxs-lookup"><span data-stu-id="4fe6d-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="4fe6d-479">PostGIS のドキュメント</span><span class="sxs-lookup"><span data-stu-id="4fe6d-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)

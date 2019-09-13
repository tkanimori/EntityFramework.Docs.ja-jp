---
title: 空間データ-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 026df735473e31f1c1463c1fbc6f46c4fd6dfd4f
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921734"
---
# <a name="spatial-data"></a><span data-ttu-id="4ee75-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="4ee75-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="4ee75-103">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="4ee75-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="4ee75-104">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="4ee75-105">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="4ee75-106">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="4ee75-107">EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4ee75-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="4ee75-108">インストール</span><span class="sxs-lookup"><span data-stu-id="4ee75-108">Installing</span></span>

<span data-ttu-id="4ee75-109">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="4ee75-110">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="4ee75-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4ee75-111">EF Core Provider</span></span>                        | <span data-ttu-id="4ee75-112">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="4ee75-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="4ee75-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4ee75-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="4ee75-114">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="4ee75-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="4ee75-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="4ee75-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="4ee75-116">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="4ee75-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="4ee75-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="4ee75-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="4ee75-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4ee75-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="4ee75-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="4ee75-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="4ee75-120">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4ee75-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="4ee75-121">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="4ee75-121">Reverse engineering</span></span>

<span data-ttu-id="4ee75-122">空間 NuGet パッケージでは、空間プロパティを使用してモデルの[リバースエンジニアリング](../managing-schemas/scaffolding.md)を行うこともできます`Scaffold-DbContext`が`dotnet ef dbcontext scaffold`、またはを実行する***前***にパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="4ee75-123">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="4ee75-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="4ee75-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="4ee75-125">NetTopologySuite は、.NET 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="4ee75-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="4ee75-126">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="4ee75-127">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="4ee75-128">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="4ee75-129">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-129">There are several spatial data types.</span></span> <span data-ttu-id="4ee75-130">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="4ee75-131">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="4ee75-132">これらは名前空間内`NetTopologySuite.Geometries`にあります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="4ee75-133">geometry</span><span class="sxs-lookup"><span data-stu-id="4ee75-133">Geometry</span></span>
  * <span data-ttu-id="4ee75-134">ポイント</span><span class="sxs-lookup"><span data-stu-id="4ee75-134">Point</span></span>
  * <span data-ttu-id="4ee75-135">LineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-135">LineString</span></span>
  * <span data-ttu-id="4ee75-136">多角形</span><span class="sxs-lookup"><span data-stu-id="4ee75-136">Polygon</span></span>
  * <span data-ttu-id="4ee75-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="4ee75-137">GeometryCollection</span></span>
    * <span data-ttu-id="4ee75-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="4ee75-138">MultiPoint</span></span>
    * <span data-ttu-id="4ee75-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-139">MultiLineString</span></span>
    * <span data-ttu-id="4ee75-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="4ee75-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="4ee75-141">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4ee75-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="4ee75-142">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="4ee75-143">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](http://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="4ee75-144">値の作成</span><span class="sxs-lookup"><span data-stu-id="4ee75-144">Creating values</span></span>

<span data-ttu-id="4ee75-145">コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4ee75-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="4ee75-146">これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用) や座標シーケンス (次元を決定する) など、より高度なものを制御できます。およびメジャーが使用可能)。</span><span class="sxs-lookup"><span data-stu-id="4ee75-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="4ee75-147">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="4ee75-148">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="4ee75-148">Longitude and Latitude</span></span>

<span data-ttu-id="4ee75-149">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="4ee75-150">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="4ee75-151">これは、通常 、これらの`latitude, longitude`値が表示されるのとは逆の形式であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4ee75-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="4ee75-152">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="4ee75-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="4ee75-153">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="4ee75-154">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="4ee75-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="4ee75-155">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="4ee75-156">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="4ee75-157">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="4ee75-158">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="4ee75-159">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="4ee75-159">Querying Data</span></span>

<span data-ttu-id="4ee75-160">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="4ee75-161">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="4ee75-162">この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="4ee75-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4ee75-163">SQL Server</span></span>

<span data-ttu-id="4ee75-164">SQL Server を使用している場合は、注意が必要な点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="4ee75-165">Geography または geometry</span><span class="sxs-lookup"><span data-stu-id="4ee75-165">Geography or geometry</span></span>

<span data-ttu-id="4ee75-166">既定では、空間プロパティは SQL Server `geography`の列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="4ee75-167">を使用`geometry`するには、モデルの[列の型を構成](xref:core/modeling/relational/data-types)します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="4ee75-168">Geography polygon リング</span><span class="sxs-lookup"><span data-stu-id="4ee75-168">Geography polygon rings</span></span>

<span data-ttu-id="4ee75-169">列の`geography`型を使用する場合、SQL Server によって、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="4ee75-170">外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="4ee75-171">NTS は、データベースに値を送信する前にこのことを検証します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="4ee75-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="4ee75-172">FullGlobe</span></span>

<span data-ttu-id="4ee75-173">SQL Server には、列の`geography`型を使用するときに全地球を表す非標準の geometry 型があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="4ee75-174">また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。</span><span class="sxs-lookup"><span data-stu-id="4ee75-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="4ee75-175">これらのいずれも、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4ee75-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="4ee75-176">NTS では、これに基づく FullGlobe と多角形はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4ee75-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="4ee75-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="4ee75-177">SQLite</span></span>

<span data-ttu-id="4ee75-178">SQLite を使用する場合の追加情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="4ee75-179">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="4ee75-179">Installing SpatiaLite</span></span>

<span data-ttu-id="4ee75-180">Windows では、ネイティブの mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="4ee75-181">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="4ee75-182">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="4ee75-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="4ee75-183">たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="4ee75-184">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="4ee75-184">Configuring SRID</span></span>

<span data-ttu-id="4ee75-185">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ee75-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="4ee75-186">既定の SRID は`0`です。</span><span class="sxs-lookup"><span data-stu-id="4ee75-186">The default SRID is `0`.</span></span> <span data-ttu-id="4ee75-187">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="4ee75-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="4ee75-188">ディメンション</span><span class="sxs-lookup"><span data-stu-id="4ee75-188">Dimension</span></span>

<span data-ttu-id="4ee75-189">SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。</span><span class="sxs-lookup"><span data-stu-id="4ee75-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="4ee75-190">既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4ee75-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="4ee75-191">翻訳された操作</span><span class="sxs-lookup"><span data-stu-id="4ee75-191">Translated Operations</span></span>

<span data-ttu-id="4ee75-192">次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="4ee75-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="4ee75-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4ee75-193">NetTopologySuite</span></span> | <span data-ttu-id="4ee75-194">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-194">SQL Server (geometry)</span></span> | <span data-ttu-id="4ee75-195">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="4ee75-195">SQL Server (geography)</span></span> | <span data-ttu-id="4ee75-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="4ee75-196">SQLite</span></span> | <span data-ttu-id="4ee75-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="4ee75-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="4ee75-198">Geometry. 面</span><span class="sxs-lookup"><span data-stu-id="4ee75-198">Geometry.Area</span></span> | <span data-ttu-id="4ee75-199">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-199">✔</span></span> | <span data-ttu-id="4ee75-200">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-200">✔</span></span> | <span data-ttu-id="4ee75-201">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-201">✔</span></span> | <span data-ttu-id="4ee75-202">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-202">✔</span></span>
<span data-ttu-id="4ee75-203">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="4ee75-204">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-204">✔</span></span> | <span data-ttu-id="4ee75-205">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-205">✔</span></span> | <span data-ttu-id="4ee75-206">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-206">✔</span></span> | <span data-ttu-id="4ee75-207">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-207">✔</span></span>
<span data-ttu-id="4ee75-208">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-208">Geometry.AsText()</span></span> | <span data-ttu-id="4ee75-209">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-209">✔</span></span> | <span data-ttu-id="4ee75-210">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-210">✔</span></span> | <span data-ttu-id="4ee75-211">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-211">✔</span></span> | <span data-ttu-id="4ee75-212">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-212">✔</span></span>
<span data-ttu-id="4ee75-213">Geometry. 境界</span><span class="sxs-lookup"><span data-stu-id="4ee75-213">Geometry.Boundary</span></span> | <span data-ttu-id="4ee75-214">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-214">✔</span></span> | | <span data-ttu-id="4ee75-215">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-215">✔</span></span> | <span data-ttu-id="4ee75-216">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-216">✔</span></span>
<span data-ttu-id="4ee75-217">Geometry. Buffer (double)</span><span class="sxs-lookup"><span data-stu-id="4ee75-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="4ee75-218">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-218">✔</span></span> | <span data-ttu-id="4ee75-219">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-219">✔</span></span> | <span data-ttu-id="4ee75-220">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-220">✔</span></span> | <span data-ttu-id="4ee75-221">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-221">✔</span></span>
<span data-ttu-id="4ee75-222">Geometry. Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="4ee75-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="4ee75-223">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-223">✔</span></span>
<span data-ttu-id="4ee75-224">Geometry. 重心</span><span class="sxs-lookup"><span data-stu-id="4ee75-224">Geometry.Centroid</span></span> | <span data-ttu-id="4ee75-225">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-225">✔</span></span> | | <span data-ttu-id="4ee75-226">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-226">✔</span></span> | <span data-ttu-id="4ee75-227">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-227">✔</span></span>
<span data-ttu-id="4ee75-228">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-228">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="4ee75-229">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-229">✔</span></span> | <span data-ttu-id="4ee75-230">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-230">✔</span></span> | <span data-ttu-id="4ee75-231">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-231">✔</span></span> | <span data-ttu-id="4ee75-232">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-232">✔</span></span>
<span data-ttu-id="4ee75-233">ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-233">Geometry.ConvexHull()</span></span> | <span data-ttu-id="4ee75-234">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-234">✔</span></span> | <span data-ttu-id="4ee75-235">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-235">✔</span></span> | <span data-ttu-id="4ee75-236">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-236">✔</span></span> | <span data-ttu-id="4ee75-237">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-237">✔</span></span>
<span data-ttu-id="4ee75-238">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-238">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="4ee75-239">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-239">✔</span></span> | <span data-ttu-id="4ee75-240">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-240">✔</span></span>
<span data-ttu-id="4ee75-241">Geometry. カバー (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-241">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="4ee75-242">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-242">✔</span></span> | <span data-ttu-id="4ee75-243">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-243">✔</span></span>
<span data-ttu-id="4ee75-244">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-244">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="4ee75-245">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-245">✔</span></span> | | <span data-ttu-id="4ee75-246">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-246">✔</span></span> | <span data-ttu-id="4ee75-247">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-247">✔</span></span>
<span data-ttu-id="4ee75-248">Geometry. 差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-248">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="4ee75-249">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-249">✔</span></span> | <span data-ttu-id="4ee75-250">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-250">✔</span></span> | <span data-ttu-id="4ee75-251">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-251">✔</span></span> | <span data-ttu-id="4ee75-252">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-252">✔</span></span>
<span data-ttu-id="4ee75-253">Geometry. ディメンション</span><span class="sxs-lookup"><span data-stu-id="4ee75-253">Geometry.Dimension</span></span> | <span data-ttu-id="4ee75-254">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-254">✔</span></span> | <span data-ttu-id="4ee75-255">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-255">✔</span></span> | <span data-ttu-id="4ee75-256">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-256">✔</span></span> | <span data-ttu-id="4ee75-257">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-257">✔</span></span>
<span data-ttu-id="4ee75-258">Geometry. 不整合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-258">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="4ee75-259">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-259">✔</span></span> | <span data-ttu-id="4ee75-260">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-260">✔</span></span> | <span data-ttu-id="4ee75-261">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-261">✔</span></span> | <span data-ttu-id="4ee75-262">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-262">✔</span></span>
<span data-ttu-id="4ee75-263">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-263">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="4ee75-264">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-264">✔</span></span> | <span data-ttu-id="4ee75-265">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-265">✔</span></span> | <span data-ttu-id="4ee75-266">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-266">✔</span></span> | <span data-ttu-id="4ee75-267">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-267">✔</span></span>
<span data-ttu-id="4ee75-268">Geometry. エンベロープ</span><span class="sxs-lookup"><span data-stu-id="4ee75-268">Geometry.Envelope</span></span> | <span data-ttu-id="4ee75-269">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-269">✔</span></span> | | <span data-ttu-id="4ee75-270">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-270">✔</span></span> | <span data-ttu-id="4ee75-271">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-271">✔</span></span>
<span data-ttu-id="4ee75-272">EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-272">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="4ee75-273">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-273">✔</span></span>
<span data-ttu-id="4ee75-274">EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-274">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="4ee75-275">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-275">✔</span></span> | <span data-ttu-id="4ee75-276">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-276">✔</span></span> | <span data-ttu-id="4ee75-277">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-277">✔</span></span> | <span data-ttu-id="4ee75-278">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-278">✔</span></span>
<span data-ttu-id="4ee75-279">GeometryType</span><span class="sxs-lookup"><span data-stu-id="4ee75-279">Geometry.GeometryType</span></span> | <span data-ttu-id="4ee75-280">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-280">✔</span></span> | <span data-ttu-id="4ee75-281">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-281">✔</span></span> | <span data-ttu-id="4ee75-282">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-282">✔</span></span> | <span data-ttu-id="4ee75-283">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-283">✔</span></span>
<span data-ttu-id="4ee75-284">GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="4ee75-284">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="4ee75-285">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-285">✔</span></span> | | <span data-ttu-id="4ee75-286">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-286">✔</span></span> | <span data-ttu-id="4ee75-287">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-287">✔</span></span>
<span data-ttu-id="4ee75-288">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="4ee75-288">Geometry.InteriorPoint</span></span> | <span data-ttu-id="4ee75-289">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-289">✔</span></span> | | <span data-ttu-id="4ee75-290">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-290">✔</span></span>
<span data-ttu-id="4ee75-291">Geometry. 積集合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-291">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="4ee75-292">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-292">✔</span></span> | <span data-ttu-id="4ee75-293">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-293">✔</span></span> | <span data-ttu-id="4ee75-294">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-294">✔</span></span> | <span data-ttu-id="4ee75-295">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-295">✔</span></span>
<span data-ttu-id="4ee75-296">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-296">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="4ee75-297">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-297">✔</span></span> | <span data-ttu-id="4ee75-298">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-298">✔</span></span> | <span data-ttu-id="4ee75-299">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-299">✔</span></span> | <span data-ttu-id="4ee75-300">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-300">✔</span></span>
<span data-ttu-id="4ee75-301">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="4ee75-301">Geometry.IsEmpty</span></span> | <span data-ttu-id="4ee75-302">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-302">✔</span></span> | <span data-ttu-id="4ee75-303">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-303">✔</span></span> | <span data-ttu-id="4ee75-304">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-304">✔</span></span> | <span data-ttu-id="4ee75-305">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-305">✔</span></span>
<span data-ttu-id="4ee75-306">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="4ee75-306">Geometry.IsSimple</span></span> | <span data-ttu-id="4ee75-307">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-307">✔</span></span> | | <span data-ttu-id="4ee75-308">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-308">✔</span></span> | <span data-ttu-id="4ee75-309">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-309">✔</span></span>
<span data-ttu-id="4ee75-310">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="4ee75-310">Geometry.IsValid</span></span> | <span data-ttu-id="4ee75-311">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-311">✔</span></span> | <span data-ttu-id="4ee75-312">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-312">✔</span></span> | <span data-ttu-id="4ee75-313">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-313">✔</span></span> | <span data-ttu-id="4ee75-314">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-314">✔</span></span>
<span data-ttu-id="4ee75-315">Geometry. Iswithin Distance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="4ee75-315">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="4ee75-316">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-316">✔</span></span> | | <span data-ttu-id="4ee75-317">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-317">✔</span></span>
<span data-ttu-id="4ee75-318">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="4ee75-318">Geometry.Length</span></span> | <span data-ttu-id="4ee75-319">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-319">✔</span></span> | <span data-ttu-id="4ee75-320">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-320">✔</span></span> | <span data-ttu-id="4ee75-321">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-321">✔</span></span> | <span data-ttu-id="4ee75-322">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-322">✔</span></span>
<span data-ttu-id="4ee75-323">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="4ee75-323">Geometry.NumGeometries</span></span> | <span data-ttu-id="4ee75-324">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-324">✔</span></span> | <span data-ttu-id="4ee75-325">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-325">✔</span></span> | <span data-ttu-id="4ee75-326">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-326">✔</span></span> | <span data-ttu-id="4ee75-327">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-327">✔</span></span>
<span data-ttu-id="4ee75-328">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="4ee75-328">Geometry.NumPoints</span></span> | <span data-ttu-id="4ee75-329">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-329">✔</span></span> | <span data-ttu-id="4ee75-330">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-330">✔</span></span> | <span data-ttu-id="4ee75-331">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-331">✔</span></span> | <span data-ttu-id="4ee75-332">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-332">✔</span></span>
<span data-ttu-id="4ee75-333">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="4ee75-333">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="4ee75-334">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-334">✔</span></span> | <span data-ttu-id="4ee75-335">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-335">✔</span></span> | <span data-ttu-id="4ee75-336">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-336">✔</span></span>
<span data-ttu-id="4ee75-337">Geometry. 重なり (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-337">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="4ee75-338">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-338">✔</span></span> | <span data-ttu-id="4ee75-339">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-339">✔</span></span> | <span data-ttu-id="4ee75-340">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-340">✔</span></span> | <span data-ttu-id="4ee75-341">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-341">✔</span></span>
<span data-ttu-id="4ee75-342">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="4ee75-342">Geometry.PointOnSurface</span></span> | <span data-ttu-id="4ee75-343">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-343">✔</span></span> | | <span data-ttu-id="4ee75-344">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-344">✔</span></span> | <span data-ttu-id="4ee75-345">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-345">✔</span></span>
<span data-ttu-id="4ee75-346">Geometry. 関連付け (Geometry, string)</span><span class="sxs-lookup"><span data-stu-id="4ee75-346">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="4ee75-347">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-347">✔</span></span> | | <span data-ttu-id="4ee75-348">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-348">✔</span></span> | <span data-ttu-id="4ee75-349">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-349">✔</span></span>
<span data-ttu-id="4ee75-350">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-350">Geometry.Reverse()</span></span> | | | <span data-ttu-id="4ee75-351">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-351">✔</span></span> | <span data-ttu-id="4ee75-352">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-352">✔</span></span>
<span data-ttu-id="4ee75-353">SRID</span><span class="sxs-lookup"><span data-stu-id="4ee75-353">Geometry.SRID</span></span> | <span data-ttu-id="4ee75-354">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-354">✔</span></span> | <span data-ttu-id="4ee75-355">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-355">✔</span></span> | <span data-ttu-id="4ee75-356">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-356">✔</span></span> | <span data-ttu-id="4ee75-357">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-357">✔</span></span>
<span data-ttu-id="4ee75-358">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-358">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="4ee75-359">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-359">✔</span></span> | <span data-ttu-id="4ee75-360">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-360">✔</span></span> | <span data-ttu-id="4ee75-361">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-361">✔</span></span> | <span data-ttu-id="4ee75-362">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-362">✔</span></span>
<span data-ttu-id="4ee75-363">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-363">Geometry.ToBinary()</span></span> | <span data-ttu-id="4ee75-364">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-364">✔</span></span> | <span data-ttu-id="4ee75-365">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-365">✔</span></span> | <span data-ttu-id="4ee75-366">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-366">✔</span></span> | <span data-ttu-id="4ee75-367">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-367">✔</span></span>
<span data-ttu-id="4ee75-368">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-368">Geometry.ToText()</span></span> | <span data-ttu-id="4ee75-369">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-369">✔</span></span> | <span data-ttu-id="4ee75-370">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-370">✔</span></span> | <span data-ttu-id="4ee75-371">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-371">✔</span></span> | <span data-ttu-id="4ee75-372">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-372">✔</span></span>
<span data-ttu-id="4ee75-373">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-373">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="4ee75-374">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-374">✔</span></span> | | <span data-ttu-id="4ee75-375">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-375">✔</span></span> | <span data-ttu-id="4ee75-376">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-376">✔</span></span>
<span data-ttu-id="4ee75-377">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="4ee75-377">Geometry.Union()</span></span> | | | <span data-ttu-id="4ee75-378">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-378">✔</span></span>
<span data-ttu-id="4ee75-379">Geometry. 共用体 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-379">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="4ee75-380">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-380">✔</span></span> | <span data-ttu-id="4ee75-381">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-381">✔</span></span> | <span data-ttu-id="4ee75-382">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-382">✔</span></span> | <span data-ttu-id="4ee75-383">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-383">✔</span></span>
<span data-ttu-id="4ee75-384">Geometry. 内 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="4ee75-384">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="4ee75-385">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-385">✔</span></span> | <span data-ttu-id="4ee75-386">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-386">✔</span></span> | <span data-ttu-id="4ee75-387">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-387">✔</span></span> | <span data-ttu-id="4ee75-388">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-388">✔</span></span>
<span data-ttu-id="4ee75-389">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="4ee75-389">GeometryCollection.Count</span></span> | <span data-ttu-id="4ee75-390">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-390">✔</span></span> | <span data-ttu-id="4ee75-391">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-391">✔</span></span> | <span data-ttu-id="4ee75-392">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-392">✔</span></span> | <span data-ttu-id="4ee75-393">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-393">✔</span></span>
<span data-ttu-id="4ee75-394">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="4ee75-394">GeometryCollection[int]</span></span> | <span data-ttu-id="4ee75-395">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-395">✔</span></span> | <span data-ttu-id="4ee75-396">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-396">✔</span></span> | <span data-ttu-id="4ee75-397">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-397">✔</span></span> | <span data-ttu-id="4ee75-398">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-398">✔</span></span>
<span data-ttu-id="4ee75-399">LineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-399">LineString.Count</span></span> | <span data-ttu-id="4ee75-400">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-400">✔</span></span> | <span data-ttu-id="4ee75-401">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-401">✔</span></span> | <span data-ttu-id="4ee75-402">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-402">✔</span></span> | <span data-ttu-id="4ee75-403">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-403">✔</span></span>
<span data-ttu-id="4ee75-404">LineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-404">LineString.EndPoint</span></span> | <span data-ttu-id="4ee75-405">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-405">✔</span></span> | <span data-ttu-id="4ee75-406">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-406">✔</span></span> | <span data-ttu-id="4ee75-407">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-407">✔</span></span> | <span data-ttu-id="4ee75-408">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-408">✔</span></span>
<span data-ttu-id="4ee75-409">LineString n (int)</span><span class="sxs-lookup"><span data-stu-id="4ee75-409">LineString.GetPointN(int)</span></span> | <span data-ttu-id="4ee75-410">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-410">✔</span></span> | <span data-ttu-id="4ee75-411">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-411">✔</span></span> | <span data-ttu-id="4ee75-412">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-412">✔</span></span> | <span data-ttu-id="4ee75-413">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-413">✔</span></span>
<span data-ttu-id="4ee75-414">LineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-414">LineString.IsClosed</span></span> | <span data-ttu-id="4ee75-415">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-415">✔</span></span> | <span data-ttu-id="4ee75-416">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-416">✔</span></span> | <span data-ttu-id="4ee75-417">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-417">✔</span></span> | <span data-ttu-id="4ee75-418">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-418">✔</span></span>
<span data-ttu-id="4ee75-419">LineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-419">LineString.IsRing</span></span> | <span data-ttu-id="4ee75-420">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-420">✔</span></span> | | <span data-ttu-id="4ee75-421">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-421">✔</span></span> | <span data-ttu-id="4ee75-422">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-422">✔</span></span>
<span data-ttu-id="4ee75-423">LineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="4ee75-423">LineString.StartPoint</span></span> | <span data-ttu-id="4ee75-424">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-424">✔</span></span> | <span data-ttu-id="4ee75-425">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-425">✔</span></span> | <span data-ttu-id="4ee75-426">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-426">✔</span></span> | <span data-ttu-id="4ee75-427">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-427">✔</span></span>
<span data-ttu-id="4ee75-428">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="4ee75-428">MultiLineString.IsClosed</span></span> | <span data-ttu-id="4ee75-429">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-429">✔</span></span> | <span data-ttu-id="4ee75-430">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-430">✔</span></span> | <span data-ttu-id="4ee75-431">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-431">✔</span></span> | <span data-ttu-id="4ee75-432">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-432">✔</span></span>
<span data-ttu-id="4ee75-433">Point. M</span><span class="sxs-lookup"><span data-stu-id="4ee75-433">Point.M</span></span> | <span data-ttu-id="4ee75-434">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-434">✔</span></span> | <span data-ttu-id="4ee75-435">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-435">✔</span></span> | <span data-ttu-id="4ee75-436">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-436">✔</span></span> | <span data-ttu-id="4ee75-437">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-437">✔</span></span>
<span data-ttu-id="4ee75-438">Point. X</span><span class="sxs-lookup"><span data-stu-id="4ee75-438">Point.X</span></span> | <span data-ttu-id="4ee75-439">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-439">✔</span></span> | <span data-ttu-id="4ee75-440">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-440">✔</span></span> | <span data-ttu-id="4ee75-441">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-441">✔</span></span> | <span data-ttu-id="4ee75-442">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-442">✔</span></span>
<span data-ttu-id="4ee75-443">Point. Y</span><span class="sxs-lookup"><span data-stu-id="4ee75-443">Point.Y</span></span> | <span data-ttu-id="4ee75-444">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-444">✔</span></span> | <span data-ttu-id="4ee75-445">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-445">✔</span></span> | <span data-ttu-id="4ee75-446">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-446">✔</span></span> | <span data-ttu-id="4ee75-447">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-447">✔</span></span>
<span data-ttu-id="4ee75-448">Point. Z</span><span class="sxs-lookup"><span data-stu-id="4ee75-448">Point.Z</span></span> | <span data-ttu-id="4ee75-449">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-449">✔</span></span> | <span data-ttu-id="4ee75-450">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-450">✔</span></span> | <span data-ttu-id="4ee75-451">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-451">✔</span></span> | <span data-ttu-id="4ee75-452">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-452">✔</span></span>
<span data-ttu-id="4ee75-453">ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="4ee75-453">Polygon.ExteriorRing</span></span> | <span data-ttu-id="4ee75-454">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-454">✔</span></span> | <span data-ttu-id="4ee75-455">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-455">✔</span></span> | <span data-ttu-id="4ee75-456">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-456">✔</span></span> | <span data-ttu-id="4ee75-457">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-457">✔</span></span>
<span data-ttu-id="4ee75-458">GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="4ee75-458">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="4ee75-459">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-459">✔</span></span> | <span data-ttu-id="4ee75-460">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-460">✔</span></span> | <span data-ttu-id="4ee75-461">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-461">✔</span></span> | <span data-ttu-id="4ee75-462">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-462">✔</span></span>
<span data-ttu-id="4ee75-463">NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="4ee75-463">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="4ee75-464">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-464">✔</span></span> | <span data-ttu-id="4ee75-465">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-465">✔</span></span> | <span data-ttu-id="4ee75-466">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-466">✔</span></span> | <span data-ttu-id="4ee75-467">✔</span><span class="sxs-lookup"><span data-stu-id="4ee75-467">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ee75-468">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4ee75-468">Additional resources</span></span>

* [<span data-ttu-id="4ee75-469">SQL Server の空間データ</span><span class="sxs-lookup"><span data-stu-id="4ee75-469">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="4ee75-470">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="4ee75-470">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="4ee75-471">Npgsql 空間のドキュメント</span><span class="sxs-lookup"><span data-stu-id="4ee75-471">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="4ee75-472">PostGIS のドキュメント</span><span class="sxs-lookup"><span data-stu-id="4ee75-472">PostGIS Documentation</span></span>](http://postgis.net/documentation/)

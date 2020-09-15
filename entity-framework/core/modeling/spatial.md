---
title: 空間データ-EF Core
description: Entity Framework Core モデルでの空間データの使用
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
uid: core/modeling/spatial
ms.openlocfilehash: 8c08835f2d6211e6be5852b58b35f003f823bded
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071434"
---
# <a name="spatial-data"></a><span data-ttu-id="e4a93-103">空間データ</span><span class="sxs-lookup"><span data-stu-id="e4a93-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="e4a93-104">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="e4a93-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="e4a93-105">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="e4a93-106">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="e4a93-107">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="e4a93-108">EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) 空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e4a93-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="e4a93-109">インストール</span><span class="sxs-lookup"><span data-stu-id="e4a93-109">Installing</span></span>

<span data-ttu-id="e4a93-110">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="e4a93-111">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="e4a93-112">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e4a93-112">EF Core Provider</span></span>                        | <span data-ttu-id="e4a93-113">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="e4a93-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="e4a93-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e4a93-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="e4a93-115">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="e4a93-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="e4a93-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e4a93-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="e4a93-117">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="e4a93-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="e4a93-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="e4a93-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="e4a93-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e4a93-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="e4a93-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="e4a93-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="e4a93-121">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="e4a93-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="e4a93-122">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="e4a93-122">Reverse engineering</span></span>

<span data-ttu-id="e4a93-123">空間 NuGet パッケージでは、空間プロパティを使用してモデルの [リバースエンジニアリング](xref:core/managing-schemas/scaffolding) を行うこともできますが、またはを実行する ***前*** にパッケージをインストールする必要があり `Scaffold-DbContext` `dotnet ef dbcontext scaffold` ます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="e4a93-124">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="e4a93-125">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="e4a93-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="e4a93-126">NetTopologySuite は、.NET 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="e4a93-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="e4a93-127">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="e4a93-128">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="e4a93-129">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="e4a93-130">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-130">There are several spatial data types.</span></span> <span data-ttu-id="e4a93-131">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="e4a93-132">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="e4a93-133">これらは名前空間内にあり `NetTopologySuite.Geometries` ます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="e4a93-134">ジオメトリ</span><span class="sxs-lookup"><span data-stu-id="e4a93-134">Geometry</span></span>
  * <span data-ttu-id="e4a93-135">ポイント</span><span class="sxs-lookup"><span data-stu-id="e4a93-135">Point</span></span>
  * <span data-ttu-id="e4a93-136">LineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-136">LineString</span></span>
  * <span data-ttu-id="e4a93-137">多角形</span><span class="sxs-lookup"><span data-stu-id="e4a93-137">Polygon</span></span>
  * <span data-ttu-id="e4a93-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e4a93-138">GeometryCollection</span></span>
    * <span data-ttu-id="e4a93-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="e4a93-139">MultiPoint</span></span>
    * <span data-ttu-id="e4a93-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-140">MultiLineString</span></span>
    * <span data-ttu-id="e4a93-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="e4a93-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="e4a93-142">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e4a93-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="e4a93-143">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="e4a93-144">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="e4a93-145">値の作成</span><span class="sxs-lookup"><span data-stu-id="e4a93-145">Creating values</span></span>

<span data-ttu-id="e4a93-146">コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e4a93-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="e4a93-147">これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用される) や座標シーケンス (使用可能な次元とメジャーを決定) など、より高度なものを制御できます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="e4a93-148">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="e4a93-149">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="e4a93-149">Longitude and Latitude</span></span>

<span data-ttu-id="e4a93-150">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="e4a93-151">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="e4a93-152">これは、通常、これらの値が表示されるのとは **逆** の形式であることに注意して `latitude, longitude` ください。</span><span class="sxs-lookup"><span data-stu-id="e4a93-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="e4a93-153">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="e4a93-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="e4a93-154">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="e4a93-155">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="e4a93-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="e4a93-156">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="e4a93-157">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="e4a93-158">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="e4a93-159">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="e4a93-160">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="e4a93-160">Querying Data</span></span>

<span data-ttu-id="e4a93-161">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="e4a93-162">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="e4a93-163">この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="e4a93-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e4a93-164">SQL Server</span></span>

<span data-ttu-id="e4a93-165">SQL Server を使用している場合は、注意が必要な点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="e4a93-166">Geography または geometry</span><span class="sxs-lookup"><span data-stu-id="e4a93-166">Geography or geometry</span></span>

<span data-ttu-id="e4a93-167">既定では、空間プロパティは SQL Server の列にマップされ `geography` ます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="e4a93-168">を使用するには `geometry` 、モデルの [列の型を構成](xref:core/modeling/entity-properties#column-data-types) します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="e4a93-169">Geography polygon リング</span><span class="sxs-lookup"><span data-stu-id="e4a93-169">Geography polygon rings</span></span>

<span data-ttu-id="e4a93-170">列の型を使用する場合、SQL Server によって `geography` 、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="e4a93-171">外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="e4a93-172">NTS は、データベースに値を送信する前にこのことを検証します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="e4a93-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="e4a93-173">FullGlobe</span></span>

<span data-ttu-id="e4a93-174">SQL Server には、列の型を使用するときに全地球を表す非標準の geometry 型があり `geography` ます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="e4a93-175">また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。</span><span class="sxs-lookup"><span data-stu-id="e4a93-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="e4a93-176">これらのいずれも、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e4a93-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="e4a93-177">NTS では、これに基づく FullGlobe と多角形はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e4a93-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="e4a93-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="e4a93-178">SQLite</span></span>

<span data-ttu-id="e4a93-179">SQLite を使用する場合の追加情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="e4a93-180">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="e4a93-180">Installing SpatiaLite</span></span>

<span data-ttu-id="e4a93-181">Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="e4a93-182">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="e4a93-183">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="e4a93-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="e4a93-184">たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="e4a93-185">残念ながら、新しいバージョンの PROJ (SpatiaLite の依存関係) は EF の既定の [SQLitePCLRaw バンドル](/dotnet/standard/data/sqlite/custom-versions#bundles)と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="e4a93-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="e4a93-186">この問題を回避するには、システム SQLite ライブラリを使用するカスタム [SQLitePCLRaw プロバイダー](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) を作成するか、または SpatiaLite のカスタムビルドをインストールして、PROJ サポートを無効にします。</span><span class="sxs-lookup"><span data-stu-id="e4a93-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="e4a93-187">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="e4a93-187">Configuring SRID</span></span>

<span data-ttu-id="e4a93-188">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4a93-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="e4a93-189">既定の SRID は `0` です。</span><span class="sxs-lookup"><span data-stu-id="e4a93-189">The default SRID is `0`.</span></span> <span data-ttu-id="e4a93-190">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="e4a93-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="e4a93-191">Dimension</span><span class="sxs-lookup"><span data-stu-id="e4a93-191">Dimension</span></span>

<span data-ttu-id="e4a93-192">SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。</span><span class="sxs-lookup"><span data-stu-id="e4a93-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="e4a93-193">既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e4a93-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="e4a93-194">翻訳された操作</span><span class="sxs-lookup"><span data-stu-id="e4a93-194">Translated Operations</span></span>

<span data-ttu-id="e4a93-195">次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="e4a93-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="e4a93-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e4a93-196">NetTopologySuite</span></span> | <span data-ttu-id="e4a93-197">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-197">SQL Server (geometry)</span></span> | <span data-ttu-id="e4a93-198">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="e4a93-198">SQL Server (geography)</span></span> | <span data-ttu-id="e4a93-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="e4a93-199">SQLite</span></span> | <span data-ttu-id="e4a93-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="e4a93-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="e4a93-201">Geometry. 面</span><span class="sxs-lookup"><span data-stu-id="e4a93-201">Geometry.Area</span></span> | <span data-ttu-id="e4a93-202">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-202">✔</span></span> | <span data-ttu-id="e4a93-203">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-203">✔</span></span> | <span data-ttu-id="e4a93-204">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-204">✔</span></span> | <span data-ttu-id="e4a93-205">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-205">✔</span></span>
<span data-ttu-id="e4a93-206">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="e4a93-207">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-207">✔</span></span> | <span data-ttu-id="e4a93-208">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-208">✔</span></span> | <span data-ttu-id="e4a93-209">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-209">✔</span></span> | <span data-ttu-id="e4a93-210">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-210">✔</span></span>
<span data-ttu-id="e4a93-211">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-211">Geometry.AsText()</span></span> | <span data-ttu-id="e4a93-212">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-212">✔</span></span> | <span data-ttu-id="e4a93-213">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-213">✔</span></span> | <span data-ttu-id="e4a93-214">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-214">✔</span></span> | <span data-ttu-id="e4a93-215">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-215">✔</span></span>
<span data-ttu-id="e4a93-216">Geometry. 境界</span><span class="sxs-lookup"><span data-stu-id="e4a93-216">Geometry.Boundary</span></span> | <span data-ttu-id="e4a93-217">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-217">✔</span></span> | | <span data-ttu-id="e4a93-218">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-218">✔</span></span> | <span data-ttu-id="e4a93-219">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-219">✔</span></span>
<span data-ttu-id="e4a93-220">Geometry. Buffer (double)</span><span class="sxs-lookup"><span data-stu-id="e4a93-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="e4a93-221">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-221">✔</span></span> | <span data-ttu-id="e4a93-222">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-222">✔</span></span> | <span data-ttu-id="e4a93-223">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-223">✔</span></span> | <span data-ttu-id="e4a93-224">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-224">✔</span></span>
<span data-ttu-id="e4a93-225">Geometry. Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="e4a93-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="e4a93-226">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-226">✔</span></span> | <span data-ttu-id="e4a93-227">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-227">✔</span></span>
<span data-ttu-id="e4a93-228">Geometry. 重心</span><span class="sxs-lookup"><span data-stu-id="e4a93-228">Geometry.Centroid</span></span> | <span data-ttu-id="e4a93-229">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-229">✔</span></span> | | <span data-ttu-id="e4a93-230">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-230">✔</span></span> | <span data-ttu-id="e4a93-231">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-231">✔</span></span>
<span data-ttu-id="e4a93-232">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="e4a93-233">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-233">✔</span></span> | <span data-ttu-id="e4a93-234">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-234">✔</span></span> | <span data-ttu-id="e4a93-235">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-235">✔</span></span> | <span data-ttu-id="e4a93-236">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-236">✔</span></span>
<span data-ttu-id="e4a93-237">ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="e4a93-238">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-238">✔</span></span> | <span data-ttu-id="e4a93-239">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-239">✔</span></span> | <span data-ttu-id="e4a93-240">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-240">✔</span></span> | <span data-ttu-id="e4a93-241">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-241">✔</span></span>
<span data-ttu-id="e4a93-242">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="e4a93-243">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-243">✔</span></span> | <span data-ttu-id="e4a93-244">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-244">✔</span></span>
<span data-ttu-id="e4a93-245">Geometry. カバー (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="e4a93-246">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-246">✔</span></span> | <span data-ttu-id="e4a93-247">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-247">✔</span></span>
<span data-ttu-id="e4a93-248">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="e4a93-249">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-249">✔</span></span> | | <span data-ttu-id="e4a93-250">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-250">✔</span></span> | <span data-ttu-id="e4a93-251">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-251">✔</span></span>
<span data-ttu-id="e4a93-252">Geometry. 差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="e4a93-253">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-253">✔</span></span> | <span data-ttu-id="e4a93-254">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-254">✔</span></span> | <span data-ttu-id="e4a93-255">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-255">✔</span></span> | <span data-ttu-id="e4a93-256">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-256">✔</span></span>
<span data-ttu-id="e4a93-257">Geometry. ディメンション</span><span class="sxs-lookup"><span data-stu-id="e4a93-257">Geometry.Dimension</span></span> | <span data-ttu-id="e4a93-258">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-258">✔</span></span> | <span data-ttu-id="e4a93-259">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-259">✔</span></span> | <span data-ttu-id="e4a93-260">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-260">✔</span></span> | <span data-ttu-id="e4a93-261">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-261">✔</span></span>
<span data-ttu-id="e4a93-262">Geometry. 不整合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="e4a93-263">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-263">✔</span></span> | <span data-ttu-id="e4a93-264">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-264">✔</span></span> | <span data-ttu-id="e4a93-265">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-265">✔</span></span> | <span data-ttu-id="e4a93-266">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-266">✔</span></span>
<span data-ttu-id="e4a93-267">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="e4a93-268">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-268">✔</span></span> | <span data-ttu-id="e4a93-269">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-269">✔</span></span> | <span data-ttu-id="e4a93-270">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-270">✔</span></span> | <span data-ttu-id="e4a93-271">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-271">✔</span></span>
<span data-ttu-id="e4a93-272">Geometry. エンベロープ</span><span class="sxs-lookup"><span data-stu-id="e4a93-272">Geometry.Envelope</span></span> | <span data-ttu-id="e4a93-273">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-273">✔</span></span> | | <span data-ttu-id="e4a93-274">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-274">✔</span></span> | <span data-ttu-id="e4a93-275">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-275">✔</span></span>
<span data-ttu-id="e4a93-276">EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="e4a93-277">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-277">✔</span></span>
<span data-ttu-id="e4a93-278">EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="e4a93-279">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-279">✔</span></span> | <span data-ttu-id="e4a93-280">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-280">✔</span></span> | <span data-ttu-id="e4a93-281">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-281">✔</span></span> | <span data-ttu-id="e4a93-282">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-282">✔</span></span>
<span data-ttu-id="e4a93-283">GeometryType</span><span class="sxs-lookup"><span data-stu-id="e4a93-283">Geometry.GeometryType</span></span> | <span data-ttu-id="e4a93-284">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-284">✔</span></span> | <span data-ttu-id="e4a93-285">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-285">✔</span></span> | <span data-ttu-id="e4a93-286">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-286">✔</span></span> | <span data-ttu-id="e4a93-287">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-287">✔</span></span>
<span data-ttu-id="e4a93-288">GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="e4a93-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="e4a93-289">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-289">✔</span></span> | | <span data-ttu-id="e4a93-290">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-290">✔</span></span> | <span data-ttu-id="e4a93-291">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-291">✔</span></span>
<span data-ttu-id="e4a93-292">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="e4a93-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="e4a93-293">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-293">✔</span></span> | | <span data-ttu-id="e4a93-294">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-294">✔</span></span> | <span data-ttu-id="e4a93-295">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-295">✔</span></span>
<span data-ttu-id="e4a93-296">Geometry. 積集合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="e4a93-297">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-297">✔</span></span> | <span data-ttu-id="e4a93-298">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-298">✔</span></span> | <span data-ttu-id="e4a93-299">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-299">✔</span></span> | <span data-ttu-id="e4a93-300">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-300">✔</span></span>
<span data-ttu-id="e4a93-301">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="e4a93-302">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-302">✔</span></span> | <span data-ttu-id="e4a93-303">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-303">✔</span></span> | <span data-ttu-id="e4a93-304">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-304">✔</span></span> | <span data-ttu-id="e4a93-305">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-305">✔</span></span>
<span data-ttu-id="e4a93-306">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="e4a93-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="e4a93-307">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-307">✔</span></span> | <span data-ttu-id="e4a93-308">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-308">✔</span></span> | <span data-ttu-id="e4a93-309">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-309">✔</span></span> | <span data-ttu-id="e4a93-310">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-310">✔</span></span>
<span data-ttu-id="e4a93-311">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="e4a93-311">Geometry.IsSimple</span></span> | <span data-ttu-id="e4a93-312">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-312">✔</span></span> | | <span data-ttu-id="e4a93-313">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-313">✔</span></span> | <span data-ttu-id="e4a93-314">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-314">✔</span></span>
<span data-ttu-id="e4a93-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="e4a93-315">Geometry.IsValid</span></span> | <span data-ttu-id="e4a93-316">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-316">✔</span></span> | <span data-ttu-id="e4a93-317">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-317">✔</span></span> | <span data-ttu-id="e4a93-318">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-318">✔</span></span> | <span data-ttu-id="e4a93-319">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-319">✔</span></span>
<span data-ttu-id="e4a93-320">Geometry. Iswithin Distance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="e4a93-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="e4a93-321">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-321">✔</span></span> | | <span data-ttu-id="e4a93-322">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-322">✔</span></span> | <span data-ttu-id="e4a93-323">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-323">✔</span></span>
<span data-ttu-id="e4a93-324">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="e4a93-324">Geometry.Length</span></span> | <span data-ttu-id="e4a93-325">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-325">✔</span></span> | <span data-ttu-id="e4a93-326">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-326">✔</span></span> | <span data-ttu-id="e4a93-327">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-327">✔</span></span> | <span data-ttu-id="e4a93-328">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-328">✔</span></span>
<span data-ttu-id="e4a93-329">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="e4a93-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="e4a93-330">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-330">✔</span></span> | <span data-ttu-id="e4a93-331">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-331">✔</span></span> | <span data-ttu-id="e4a93-332">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-332">✔</span></span> | <span data-ttu-id="e4a93-333">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-333">✔</span></span>
<span data-ttu-id="e4a93-334">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="e4a93-334">Geometry.NumPoints</span></span> | <span data-ttu-id="e4a93-335">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-335">✔</span></span> | <span data-ttu-id="e4a93-336">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-336">✔</span></span> | <span data-ttu-id="e4a93-337">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-337">✔</span></span> | <span data-ttu-id="e4a93-338">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-338">✔</span></span>
<span data-ttu-id="e4a93-339">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="e4a93-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="e4a93-340">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-340">✔</span></span> | <span data-ttu-id="e4a93-341">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-341">✔</span></span> | <span data-ttu-id="e4a93-342">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-342">✔</span></span> | <span data-ttu-id="e4a93-343">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-343">✔</span></span>
<span data-ttu-id="e4a93-344">Geometry. 重なり (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="e4a93-345">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-345">✔</span></span> | <span data-ttu-id="e4a93-346">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-346">✔</span></span> | <span data-ttu-id="e4a93-347">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-347">✔</span></span> | <span data-ttu-id="e4a93-348">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-348">✔</span></span>
<span data-ttu-id="e4a93-349">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="e4a93-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="e4a93-350">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-350">✔</span></span> | | <span data-ttu-id="e4a93-351">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-351">✔</span></span> | <span data-ttu-id="e4a93-352">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-352">✔</span></span>
<span data-ttu-id="e4a93-353">Geometry. 関連付け (Geometry, string)</span><span class="sxs-lookup"><span data-stu-id="e4a93-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="e4a93-354">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-354">✔</span></span> | | <span data-ttu-id="e4a93-355">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-355">✔</span></span> | <span data-ttu-id="e4a93-356">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-356">✔</span></span>
<span data-ttu-id="e4a93-357">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="e4a93-358">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-358">✔</span></span> | <span data-ttu-id="e4a93-359">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-359">✔</span></span>
<span data-ttu-id="e4a93-360">SRID</span><span class="sxs-lookup"><span data-stu-id="e4a93-360">Geometry.SRID</span></span> | <span data-ttu-id="e4a93-361">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-361">✔</span></span> | <span data-ttu-id="e4a93-362">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-362">✔</span></span> | <span data-ttu-id="e4a93-363">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-363">✔</span></span> | <span data-ttu-id="e4a93-364">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-364">✔</span></span>
<span data-ttu-id="e4a93-365">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="e4a93-366">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-366">✔</span></span> | <span data-ttu-id="e4a93-367">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-367">✔</span></span> | <span data-ttu-id="e4a93-368">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-368">✔</span></span> | <span data-ttu-id="e4a93-369">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-369">✔</span></span>
<span data-ttu-id="e4a93-370">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="e4a93-371">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-371">✔</span></span> | <span data-ttu-id="e4a93-372">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-372">✔</span></span> | <span data-ttu-id="e4a93-373">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-373">✔</span></span> | <span data-ttu-id="e4a93-374">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-374">✔</span></span>
<span data-ttu-id="e4a93-375">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-375">Geometry.ToText()</span></span> | <span data-ttu-id="e4a93-376">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-376">✔</span></span> | <span data-ttu-id="e4a93-377">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-377">✔</span></span> | <span data-ttu-id="e4a93-378">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-378">✔</span></span> | <span data-ttu-id="e4a93-379">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-379">✔</span></span>
<span data-ttu-id="e4a93-380">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="e4a93-381">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-381">✔</span></span> | | <span data-ttu-id="e4a93-382">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-382">✔</span></span> | <span data-ttu-id="e4a93-383">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-383">✔</span></span>
<span data-ttu-id="e4a93-384">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="e4a93-384">Geometry.Union()</span></span> | | | <span data-ttu-id="e4a93-385">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-385">✔</span></span> | <span data-ttu-id="e4a93-386">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-386">✔</span></span>
<span data-ttu-id="e4a93-387">Geometry. 共用体 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="e4a93-388">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-388">✔</span></span> | <span data-ttu-id="e4a93-389">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-389">✔</span></span> | <span data-ttu-id="e4a93-390">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-390">✔</span></span> | <span data-ttu-id="e4a93-391">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-391">✔</span></span>
<span data-ttu-id="e4a93-392">Geometry. 内 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e4a93-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="e4a93-393">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-393">✔</span></span> | <span data-ttu-id="e4a93-394">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-394">✔</span></span> | <span data-ttu-id="e4a93-395">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-395">✔</span></span> | <span data-ttu-id="e4a93-396">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-396">✔</span></span>
<span data-ttu-id="e4a93-397">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e4a93-397">GeometryCollection.Count</span></span> | <span data-ttu-id="e4a93-398">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-398">✔</span></span> | <span data-ttu-id="e4a93-399">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-399">✔</span></span> | <span data-ttu-id="e4a93-400">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-400">✔</span></span> | <span data-ttu-id="e4a93-401">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-401">✔</span></span>
<span data-ttu-id="e4a93-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="e4a93-402">GeometryCollection[int]</span></span> | <span data-ttu-id="e4a93-403">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-403">✔</span></span> | <span data-ttu-id="e4a93-404">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-404">✔</span></span> | <span data-ttu-id="e4a93-405">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-405">✔</span></span> | <span data-ttu-id="e4a93-406">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-406">✔</span></span>
<span data-ttu-id="e4a93-407">LineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-407">LineString.Count</span></span> | <span data-ttu-id="e4a93-408">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-408">✔</span></span> | <span data-ttu-id="e4a93-409">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-409">✔</span></span> | <span data-ttu-id="e4a93-410">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-410">✔</span></span> | <span data-ttu-id="e4a93-411">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-411">✔</span></span>
<span data-ttu-id="e4a93-412">LineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-412">LineString.EndPoint</span></span> | <span data-ttu-id="e4a93-413">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-413">✔</span></span> | <span data-ttu-id="e4a93-414">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-414">✔</span></span> | <span data-ttu-id="e4a93-415">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-415">✔</span></span> | <span data-ttu-id="e4a93-416">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-416">✔</span></span>
<span data-ttu-id="e4a93-417">LineString n (int)</span><span class="sxs-lookup"><span data-stu-id="e4a93-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="e4a93-418">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-418">✔</span></span> | <span data-ttu-id="e4a93-419">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-419">✔</span></span> | <span data-ttu-id="e4a93-420">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-420">✔</span></span> | <span data-ttu-id="e4a93-421">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-421">✔</span></span>
<span data-ttu-id="e4a93-422">LineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-422">LineString.IsClosed</span></span> | <span data-ttu-id="e4a93-423">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-423">✔</span></span> | <span data-ttu-id="e4a93-424">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-424">✔</span></span> | <span data-ttu-id="e4a93-425">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-425">✔</span></span> | <span data-ttu-id="e4a93-426">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-426">✔</span></span>
<span data-ttu-id="e4a93-427">LineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-427">LineString.IsRing</span></span> | <span data-ttu-id="e4a93-428">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-428">✔</span></span> | | <span data-ttu-id="e4a93-429">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-429">✔</span></span> | <span data-ttu-id="e4a93-430">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-430">✔</span></span>
<span data-ttu-id="e4a93-431">LineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="e4a93-431">LineString.StartPoint</span></span> | <span data-ttu-id="e4a93-432">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-432">✔</span></span> | <span data-ttu-id="e4a93-433">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-433">✔</span></span> | <span data-ttu-id="e4a93-434">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-434">✔</span></span> | <span data-ttu-id="e4a93-435">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-435">✔</span></span>
<span data-ttu-id="e4a93-436">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e4a93-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="e4a93-437">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-437">✔</span></span> | <span data-ttu-id="e4a93-438">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-438">✔</span></span> | <span data-ttu-id="e4a93-439">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-439">✔</span></span> | <span data-ttu-id="e4a93-440">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-440">✔</span></span>
<span data-ttu-id="e4a93-441">Point. M</span><span class="sxs-lookup"><span data-stu-id="e4a93-441">Point.M</span></span> | <span data-ttu-id="e4a93-442">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-442">✔</span></span> | <span data-ttu-id="e4a93-443">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-443">✔</span></span> | <span data-ttu-id="e4a93-444">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-444">✔</span></span> | <span data-ttu-id="e4a93-445">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-445">✔</span></span>
<span data-ttu-id="e4a93-446">Point. X</span><span class="sxs-lookup"><span data-stu-id="e4a93-446">Point.X</span></span> | <span data-ttu-id="e4a93-447">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-447">✔</span></span> | <span data-ttu-id="e4a93-448">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-448">✔</span></span> | <span data-ttu-id="e4a93-449">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-449">✔</span></span> | <span data-ttu-id="e4a93-450">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-450">✔</span></span>
<span data-ttu-id="e4a93-451">Point. Y</span><span class="sxs-lookup"><span data-stu-id="e4a93-451">Point.Y</span></span> | <span data-ttu-id="e4a93-452">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-452">✔</span></span> | <span data-ttu-id="e4a93-453">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-453">✔</span></span> | <span data-ttu-id="e4a93-454">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-454">✔</span></span> | <span data-ttu-id="e4a93-455">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-455">✔</span></span>
<span data-ttu-id="e4a93-456">Point. Z</span><span class="sxs-lookup"><span data-stu-id="e4a93-456">Point.Z</span></span> | <span data-ttu-id="e4a93-457">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-457">✔</span></span> | <span data-ttu-id="e4a93-458">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-458">✔</span></span> | <span data-ttu-id="e4a93-459">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-459">✔</span></span> | <span data-ttu-id="e4a93-460">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-460">✔</span></span>
<span data-ttu-id="e4a93-461">ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="e4a93-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="e4a93-462">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-462">✔</span></span> | <span data-ttu-id="e4a93-463">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-463">✔</span></span> | <span data-ttu-id="e4a93-464">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-464">✔</span></span> | <span data-ttu-id="e4a93-465">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-465">✔</span></span>
<span data-ttu-id="e4a93-466">GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="e4a93-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="e4a93-467">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-467">✔</span></span> | <span data-ttu-id="e4a93-468">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-468">✔</span></span> | <span data-ttu-id="e4a93-469">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-469">✔</span></span> | <span data-ttu-id="e4a93-470">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-470">✔</span></span>
<span data-ttu-id="e4a93-471">NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="e4a93-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="e4a93-472">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-472">✔</span></span> | <span data-ttu-id="e4a93-473">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-473">✔</span></span> | <span data-ttu-id="e4a93-474">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-474">✔</span></span> | <span data-ttu-id="e4a93-475">✔</span><span class="sxs-lookup"><span data-stu-id="e4a93-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4a93-476">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e4a93-476">Additional resources</span></span>

* [<span data-ttu-id="e4a93-477">SQL Server の空間データ</span><span class="sxs-lookup"><span data-stu-id="e4a93-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="e4a93-478">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="e4a93-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="e4a93-479">Npgsql 空間のドキュメント</span><span class="sxs-lookup"><span data-stu-id="e4a93-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="e4a93-480">PostGIS のドキュメント</span><span class="sxs-lookup"><span data-stu-id="e4a93-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)

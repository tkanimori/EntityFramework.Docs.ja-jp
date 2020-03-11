---
title: 空間データ-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 5b45f83ca7f02665f52ccfe16b5af506a6046a62
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414705"
---
# <a name="spatial-data"></a><span data-ttu-id="01066-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="01066-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="01066-103">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="01066-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="01066-104">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="01066-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="01066-105">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="01066-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="01066-106">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="01066-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="01066-107">EF Core では、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="01066-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="01066-108">インストール</span><span class="sxs-lookup"><span data-stu-id="01066-108">Installing</span></span>

<span data-ttu-id="01066-109">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="01066-110">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="01066-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="01066-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="01066-111">EF Core Provider</span></span>                        | <span data-ttu-id="01066-112">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="01066-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="01066-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="01066-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="01066-114">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="01066-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="01066-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="01066-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="01066-116">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="01066-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="01066-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="01066-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="01066-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="01066-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="01066-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="01066-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="01066-120">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="01066-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="01066-121">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="01066-121">Reverse engineering</span></span>

<span data-ttu-id="01066-122">空間 NuGet パッケージでは、空間プロパティでモデルの[リバースエンジニアリング](../managing-schemas/scaffolding.md)を行うこともできますが、`Scaffold-DbContext` または `dotnet ef dbcontext scaffold`を実行する***前***にパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="01066-123">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="01066-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="01066-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="01066-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="01066-125">NetTopologySuite は、.NET 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="01066-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="01066-126">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="01066-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="01066-127">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01066-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="01066-128">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01066-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="01066-129">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="01066-129">There are several spatial data types.</span></span> <span data-ttu-id="01066-130">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="01066-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="01066-131">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="01066-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="01066-132">これらは `NetTopologySuite.Geometries` 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="01066-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="01066-133">ジオメトリ</span><span class="sxs-lookup"><span data-stu-id="01066-133">Geometry</span></span>
  * <span data-ttu-id="01066-134">ポイント</span><span class="sxs-lookup"><span data-stu-id="01066-134">Point</span></span>
  * <span data-ttu-id="01066-135">LineString</span><span class="sxs-lookup"><span data-stu-id="01066-135">LineString</span></span>
  * <span data-ttu-id="01066-136">多角形</span><span class="sxs-lookup"><span data-stu-id="01066-136">Polygon</span></span>
  * <span data-ttu-id="01066-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="01066-137">GeometryCollection</span></span>
    * <span data-ttu-id="01066-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="01066-138">MultiPoint</span></span>
    * <span data-ttu-id="01066-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="01066-139">MultiLineString</span></span>
    * <span data-ttu-id="01066-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="01066-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="01066-141">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="01066-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="01066-142">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="01066-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="01066-143">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="01066-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="01066-144">値の作成</span><span class="sxs-lookup"><span data-stu-id="01066-144">Creating values</span></span>

<span data-ttu-id="01066-145">コンストラクターを使用して、geometry オブジェクトを作成できます。ただし、NTS では、代わりに geometry ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01066-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="01066-146">これにより、既定の SRID (座標によって使用される空間参照システム) を指定できます。また、精度モデル (計算時に使用) や座標シーケンス (次元を決定する) など、より高度なものを制御できます。およびメジャーが使用可能)。</span><span class="sxs-lookup"><span data-stu-id="01066-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="01066-147">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="01066-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="01066-148">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="01066-148">Longitude and Latitude</span></span>

<span data-ttu-id="01066-149">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="01066-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="01066-150">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="01066-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="01066-151">これは、通常、これらの値が表示される `latitude, longitude` 形式とは**逆**になっていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="01066-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="01066-152">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="01066-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="01066-153">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="01066-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="01066-154">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="01066-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="01066-155">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="01066-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="01066-156">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="01066-157">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="01066-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="01066-158">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="01066-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="01066-159">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="01066-159">Querying Data</span></span>

<span data-ttu-id="01066-160">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="01066-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="01066-161">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="01066-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="01066-162">この記事の最後にある表では、さまざまな EF Core プロバイダーでサポートされているメンバーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="01066-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="01066-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="01066-163">SQL Server</span></span>

<span data-ttu-id="01066-164">SQL Server を使用している場合は、注意が必要な点がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="01066-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="01066-165">Geography または geometry</span><span class="sxs-lookup"><span data-stu-id="01066-165">Geography or geometry</span></span>

<span data-ttu-id="01066-166">既定では、空間プロパティは SQL Server の `geography` 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="01066-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="01066-167">`geometry`を使用するには、モデルの[列の型を構成](xref:core/modeling/entity-properties#column-data-types)します。</span><span class="sxs-lookup"><span data-stu-id="01066-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="01066-168">Geography polygon リング</span><span class="sxs-lookup"><span data-stu-id="01066-168">Geography polygon rings</span></span>

<span data-ttu-id="01066-169">`geography` 列の型を使用する場合、SQL Server によって、外部リング (またはシェル) と内部リング (または穴) に追加の要件が課されます。</span><span class="sxs-lookup"><span data-stu-id="01066-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="01066-170">外部リングの方向を反時計回りにし、内部リングを時計回りにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="01066-171">NTS は、データベースに値を送信する前にこのことを検証します。</span><span class="sxs-lookup"><span data-stu-id="01066-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="01066-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="01066-172">FullGlobe</span></span>

<span data-ttu-id="01066-173">`geography` 列の型を使用する場合、SQL Server には、全地球を表す非標準の geometry 型があります。</span><span class="sxs-lookup"><span data-stu-id="01066-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="01066-174">また、地球全体に基づくポリゴンを表す方法も用意されています (外部リングは不要)。</span><span class="sxs-lookup"><span data-stu-id="01066-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="01066-175">これらのいずれも、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="01066-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="01066-176">NTS では、これに基づく FullGlobe と多角形はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="01066-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="01066-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="01066-177">SQLite</span></span>

<span data-ttu-id="01066-178">SQLite を使用する場合の追加情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="01066-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="01066-179">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="01066-179">Installing SpatiaLite</span></span>

<span data-ttu-id="01066-180">Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="01066-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="01066-181">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="01066-182">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="01066-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="01066-183">たとえば、MacOS では、APT on Ubuntu と Homebrew を使用できます。</span><span class="sxs-lookup"><span data-stu-id="01066-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="01066-184">残念ながら、新しいバージョンの PROJ (SpatiaLite の依存関係) は EF の既定の[SQLitePCLRaw バンドル](/dotnet/standard/data/sqlite/custom-versions#bundles)と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="01066-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="01066-185">この問題を回避するには、システム SQLite ライブラリを使用するカスタム[SQLitePCLRaw プロバイダー](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers)を作成するか、または SpatiaLite のカスタムビルドをインストールして、PROJ サポートを無効にします。</span><span class="sxs-lookup"><span data-stu-id="01066-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="01066-186">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="01066-186">Configuring SRID</span></span>

<span data-ttu-id="01066-187">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01066-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="01066-188">既定の SRID は `0`です。</span><span class="sxs-lookup"><span data-stu-id="01066-188">The default SRID is `0`.</span></span> <span data-ttu-id="01066-189">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="01066-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="01066-190">Dimension</span><span class="sxs-lookup"><span data-stu-id="01066-190">Dimension</span></span>

<span data-ttu-id="01066-191">SRID と同様に、列のディメンション (または座標) も列の一部として指定されます。</span><span class="sxs-lookup"><span data-stu-id="01066-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="01066-192">既定の座標は X と Y です。 ForSqliteHasDimension メソッドを使用して、追加の座標 (Z および M) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="01066-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="01066-193">翻訳された操作</span><span class="sxs-lookup"><span data-stu-id="01066-193">Translated Operations</span></span>

<span data-ttu-id="01066-194">次の表は、各 EF Core プロバイダーによってどの NTS メンバーが SQL に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="01066-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="01066-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="01066-195">NetTopologySuite</span></span> | <span data-ttu-id="01066-196">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-196">SQL Server (geometry)</span></span> | <span data-ttu-id="01066-197">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="01066-197">SQL Server (geography)</span></span> | <span data-ttu-id="01066-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="01066-198">SQLite</span></span> | <span data-ttu-id="01066-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="01066-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="01066-200">Geometry. 面</span><span class="sxs-lookup"><span data-stu-id="01066-200">Geometry.Area</span></span> | <span data-ttu-id="01066-201">✔</span><span class="sxs-lookup"><span data-stu-id="01066-201">✔</span></span> | <span data-ttu-id="01066-202">✔</span><span class="sxs-lookup"><span data-stu-id="01066-202">✔</span></span> | <span data-ttu-id="01066-203">✔</span><span class="sxs-lookup"><span data-stu-id="01066-203">✔</span></span> | <span data-ttu-id="01066-204">✔</span><span class="sxs-lookup"><span data-stu-id="01066-204">✔</span></span>
<span data-ttu-id="01066-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="01066-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="01066-206">✔</span><span class="sxs-lookup"><span data-stu-id="01066-206">✔</span></span> | <span data-ttu-id="01066-207">✔</span><span class="sxs-lookup"><span data-stu-id="01066-207">✔</span></span> | <span data-ttu-id="01066-208">✔</span><span class="sxs-lookup"><span data-stu-id="01066-208">✔</span></span> | <span data-ttu-id="01066-209">✔</span><span class="sxs-lookup"><span data-stu-id="01066-209">✔</span></span>
<span data-ttu-id="01066-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="01066-210">Geometry.AsText()</span></span> | <span data-ttu-id="01066-211">✔</span><span class="sxs-lookup"><span data-stu-id="01066-211">✔</span></span> | <span data-ttu-id="01066-212">✔</span><span class="sxs-lookup"><span data-stu-id="01066-212">✔</span></span> | <span data-ttu-id="01066-213">✔</span><span class="sxs-lookup"><span data-stu-id="01066-213">✔</span></span> | <span data-ttu-id="01066-214">✔</span><span class="sxs-lookup"><span data-stu-id="01066-214">✔</span></span>
<span data-ttu-id="01066-215">Geometry. 境界</span><span class="sxs-lookup"><span data-stu-id="01066-215">Geometry.Boundary</span></span> | <span data-ttu-id="01066-216">✔</span><span class="sxs-lookup"><span data-stu-id="01066-216">✔</span></span> | | <span data-ttu-id="01066-217">✔</span><span class="sxs-lookup"><span data-stu-id="01066-217">✔</span></span> | <span data-ttu-id="01066-218">✔</span><span class="sxs-lookup"><span data-stu-id="01066-218">✔</span></span>
<span data-ttu-id="01066-219">Geometry. Buffer (double)</span><span class="sxs-lookup"><span data-stu-id="01066-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="01066-220">✔</span><span class="sxs-lookup"><span data-stu-id="01066-220">✔</span></span> | <span data-ttu-id="01066-221">✔</span><span class="sxs-lookup"><span data-stu-id="01066-221">✔</span></span> | <span data-ttu-id="01066-222">✔</span><span class="sxs-lookup"><span data-stu-id="01066-222">✔</span></span> | <span data-ttu-id="01066-223">✔</span><span class="sxs-lookup"><span data-stu-id="01066-223">✔</span></span>
<span data-ttu-id="01066-224">Geometry. Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="01066-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="01066-225">✔</span><span class="sxs-lookup"><span data-stu-id="01066-225">✔</span></span> | <span data-ttu-id="01066-226">✔</span><span class="sxs-lookup"><span data-stu-id="01066-226">✔</span></span>
<span data-ttu-id="01066-227">Geometry. 重心</span><span class="sxs-lookup"><span data-stu-id="01066-227">Geometry.Centroid</span></span> | <span data-ttu-id="01066-228">✔</span><span class="sxs-lookup"><span data-stu-id="01066-228">✔</span></span> | | <span data-ttu-id="01066-229">✔</span><span class="sxs-lookup"><span data-stu-id="01066-229">✔</span></span> | <span data-ttu-id="01066-230">✔</span><span class="sxs-lookup"><span data-stu-id="01066-230">✔</span></span>
<span data-ttu-id="01066-231">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="01066-232">✔</span><span class="sxs-lookup"><span data-stu-id="01066-232">✔</span></span> | <span data-ttu-id="01066-233">✔</span><span class="sxs-lookup"><span data-stu-id="01066-233">✔</span></span> | <span data-ttu-id="01066-234">✔</span><span class="sxs-lookup"><span data-stu-id="01066-234">✔</span></span> | <span data-ttu-id="01066-235">✔</span><span class="sxs-lookup"><span data-stu-id="01066-235">✔</span></span>
<span data-ttu-id="01066-236">ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="01066-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="01066-237">✔</span><span class="sxs-lookup"><span data-stu-id="01066-237">✔</span></span> | <span data-ttu-id="01066-238">✔</span><span class="sxs-lookup"><span data-stu-id="01066-238">✔</span></span> | <span data-ttu-id="01066-239">✔</span><span class="sxs-lookup"><span data-stu-id="01066-239">✔</span></span> | <span data-ttu-id="01066-240">✔</span><span class="sxs-lookup"><span data-stu-id="01066-240">✔</span></span>
<span data-ttu-id="01066-241">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="01066-242">✔</span><span class="sxs-lookup"><span data-stu-id="01066-242">✔</span></span> | <span data-ttu-id="01066-243">✔</span><span class="sxs-lookup"><span data-stu-id="01066-243">✔</span></span>
<span data-ttu-id="01066-244">Geometry. カバー (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="01066-245">✔</span><span class="sxs-lookup"><span data-stu-id="01066-245">✔</span></span> | <span data-ttu-id="01066-246">✔</span><span class="sxs-lookup"><span data-stu-id="01066-246">✔</span></span>
<span data-ttu-id="01066-247">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="01066-248">✔</span><span class="sxs-lookup"><span data-stu-id="01066-248">✔</span></span> | | <span data-ttu-id="01066-249">✔</span><span class="sxs-lookup"><span data-stu-id="01066-249">✔</span></span> | <span data-ttu-id="01066-250">✔</span><span class="sxs-lookup"><span data-stu-id="01066-250">✔</span></span>
<span data-ttu-id="01066-251">Geometry. 差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="01066-252">✔</span><span class="sxs-lookup"><span data-stu-id="01066-252">✔</span></span> | <span data-ttu-id="01066-253">✔</span><span class="sxs-lookup"><span data-stu-id="01066-253">✔</span></span> | <span data-ttu-id="01066-254">✔</span><span class="sxs-lookup"><span data-stu-id="01066-254">✔</span></span> | <span data-ttu-id="01066-255">✔</span><span class="sxs-lookup"><span data-stu-id="01066-255">✔</span></span>
<span data-ttu-id="01066-256">Geometry. ディメンション</span><span class="sxs-lookup"><span data-stu-id="01066-256">Geometry.Dimension</span></span> | <span data-ttu-id="01066-257">✔</span><span class="sxs-lookup"><span data-stu-id="01066-257">✔</span></span> | <span data-ttu-id="01066-258">✔</span><span class="sxs-lookup"><span data-stu-id="01066-258">✔</span></span> | <span data-ttu-id="01066-259">✔</span><span class="sxs-lookup"><span data-stu-id="01066-259">✔</span></span> | <span data-ttu-id="01066-260">✔</span><span class="sxs-lookup"><span data-stu-id="01066-260">✔</span></span>
<span data-ttu-id="01066-261">Geometry. 不整合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="01066-262">✔</span><span class="sxs-lookup"><span data-stu-id="01066-262">✔</span></span> | <span data-ttu-id="01066-263">✔</span><span class="sxs-lookup"><span data-stu-id="01066-263">✔</span></span> | <span data-ttu-id="01066-264">✔</span><span class="sxs-lookup"><span data-stu-id="01066-264">✔</span></span> | <span data-ttu-id="01066-265">✔</span><span class="sxs-lookup"><span data-stu-id="01066-265">✔</span></span>
<span data-ttu-id="01066-266">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="01066-267">✔</span><span class="sxs-lookup"><span data-stu-id="01066-267">✔</span></span> | <span data-ttu-id="01066-268">✔</span><span class="sxs-lookup"><span data-stu-id="01066-268">✔</span></span> | <span data-ttu-id="01066-269">✔</span><span class="sxs-lookup"><span data-stu-id="01066-269">✔</span></span> | <span data-ttu-id="01066-270">✔</span><span class="sxs-lookup"><span data-stu-id="01066-270">✔</span></span>
<span data-ttu-id="01066-271">Geometry. エンベロープ</span><span class="sxs-lookup"><span data-stu-id="01066-271">Geometry.Envelope</span></span> | <span data-ttu-id="01066-272">✔</span><span class="sxs-lookup"><span data-stu-id="01066-272">✔</span></span> | | <span data-ttu-id="01066-273">✔</span><span class="sxs-lookup"><span data-stu-id="01066-273">✔</span></span> | <span data-ttu-id="01066-274">✔</span><span class="sxs-lookup"><span data-stu-id="01066-274">✔</span></span>
<span data-ttu-id="01066-275">EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="01066-276">✔</span><span class="sxs-lookup"><span data-stu-id="01066-276">✔</span></span>
<span data-ttu-id="01066-277">EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="01066-278">✔</span><span class="sxs-lookup"><span data-stu-id="01066-278">✔</span></span> | <span data-ttu-id="01066-279">✔</span><span class="sxs-lookup"><span data-stu-id="01066-279">✔</span></span> | <span data-ttu-id="01066-280">✔</span><span class="sxs-lookup"><span data-stu-id="01066-280">✔</span></span> | <span data-ttu-id="01066-281">✔</span><span class="sxs-lookup"><span data-stu-id="01066-281">✔</span></span>
<span data-ttu-id="01066-282">GeometryType</span><span class="sxs-lookup"><span data-stu-id="01066-282">Geometry.GeometryType</span></span> | <span data-ttu-id="01066-283">✔</span><span class="sxs-lookup"><span data-stu-id="01066-283">✔</span></span> | <span data-ttu-id="01066-284">✔</span><span class="sxs-lookup"><span data-stu-id="01066-284">✔</span></span> | <span data-ttu-id="01066-285">✔</span><span class="sxs-lookup"><span data-stu-id="01066-285">✔</span></span> | <span data-ttu-id="01066-286">✔</span><span class="sxs-lookup"><span data-stu-id="01066-286">✔</span></span>
<span data-ttu-id="01066-287">GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="01066-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="01066-288">✔</span><span class="sxs-lookup"><span data-stu-id="01066-288">✔</span></span> | | <span data-ttu-id="01066-289">✔</span><span class="sxs-lookup"><span data-stu-id="01066-289">✔</span></span> | <span data-ttu-id="01066-290">✔</span><span class="sxs-lookup"><span data-stu-id="01066-290">✔</span></span>
<span data-ttu-id="01066-291">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="01066-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="01066-292">✔</span><span class="sxs-lookup"><span data-stu-id="01066-292">✔</span></span> | | <span data-ttu-id="01066-293">✔</span><span class="sxs-lookup"><span data-stu-id="01066-293">✔</span></span> | <span data-ttu-id="01066-294">✔</span><span class="sxs-lookup"><span data-stu-id="01066-294">✔</span></span>
<span data-ttu-id="01066-295">Geometry. 積集合 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="01066-296">✔</span><span class="sxs-lookup"><span data-stu-id="01066-296">✔</span></span> | <span data-ttu-id="01066-297">✔</span><span class="sxs-lookup"><span data-stu-id="01066-297">✔</span></span> | <span data-ttu-id="01066-298">✔</span><span class="sxs-lookup"><span data-stu-id="01066-298">✔</span></span> | <span data-ttu-id="01066-299">✔</span><span class="sxs-lookup"><span data-stu-id="01066-299">✔</span></span>
<span data-ttu-id="01066-300">Geometry. 交差 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="01066-301">✔</span><span class="sxs-lookup"><span data-stu-id="01066-301">✔</span></span> | <span data-ttu-id="01066-302">✔</span><span class="sxs-lookup"><span data-stu-id="01066-302">✔</span></span> | <span data-ttu-id="01066-303">✔</span><span class="sxs-lookup"><span data-stu-id="01066-303">✔</span></span> | <span data-ttu-id="01066-304">✔</span><span class="sxs-lookup"><span data-stu-id="01066-304">✔</span></span>
<span data-ttu-id="01066-305">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="01066-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="01066-306">✔</span><span class="sxs-lookup"><span data-stu-id="01066-306">✔</span></span> | <span data-ttu-id="01066-307">✔</span><span class="sxs-lookup"><span data-stu-id="01066-307">✔</span></span> | <span data-ttu-id="01066-308">✔</span><span class="sxs-lookup"><span data-stu-id="01066-308">✔</span></span> | <span data-ttu-id="01066-309">✔</span><span class="sxs-lookup"><span data-stu-id="01066-309">✔</span></span>
<span data-ttu-id="01066-310">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="01066-310">Geometry.IsSimple</span></span> | <span data-ttu-id="01066-311">✔</span><span class="sxs-lookup"><span data-stu-id="01066-311">✔</span></span> | | <span data-ttu-id="01066-312">✔</span><span class="sxs-lookup"><span data-stu-id="01066-312">✔</span></span> | <span data-ttu-id="01066-313">✔</span><span class="sxs-lookup"><span data-stu-id="01066-313">✔</span></span>
<span data-ttu-id="01066-314">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="01066-314">Geometry.IsValid</span></span> | <span data-ttu-id="01066-315">✔</span><span class="sxs-lookup"><span data-stu-id="01066-315">✔</span></span> | <span data-ttu-id="01066-316">✔</span><span class="sxs-lookup"><span data-stu-id="01066-316">✔</span></span> | <span data-ttu-id="01066-317">✔</span><span class="sxs-lookup"><span data-stu-id="01066-317">✔</span></span> | <span data-ttu-id="01066-318">✔</span><span class="sxs-lookup"><span data-stu-id="01066-318">✔</span></span>
<span data-ttu-id="01066-319">Geometry. Iswithin Distance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="01066-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="01066-320">✔</span><span class="sxs-lookup"><span data-stu-id="01066-320">✔</span></span> | | <span data-ttu-id="01066-321">✔</span><span class="sxs-lookup"><span data-stu-id="01066-321">✔</span></span> | <span data-ttu-id="01066-322">✔</span><span class="sxs-lookup"><span data-stu-id="01066-322">✔</span></span>
<span data-ttu-id="01066-323">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="01066-323">Geometry.Length</span></span> | <span data-ttu-id="01066-324">✔</span><span class="sxs-lookup"><span data-stu-id="01066-324">✔</span></span> | <span data-ttu-id="01066-325">✔</span><span class="sxs-lookup"><span data-stu-id="01066-325">✔</span></span> | <span data-ttu-id="01066-326">✔</span><span class="sxs-lookup"><span data-stu-id="01066-326">✔</span></span> | <span data-ttu-id="01066-327">✔</span><span class="sxs-lookup"><span data-stu-id="01066-327">✔</span></span>
<span data-ttu-id="01066-328">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="01066-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="01066-329">✔</span><span class="sxs-lookup"><span data-stu-id="01066-329">✔</span></span> | <span data-ttu-id="01066-330">✔</span><span class="sxs-lookup"><span data-stu-id="01066-330">✔</span></span> | <span data-ttu-id="01066-331">✔</span><span class="sxs-lookup"><span data-stu-id="01066-331">✔</span></span> | <span data-ttu-id="01066-332">✔</span><span class="sxs-lookup"><span data-stu-id="01066-332">✔</span></span>
<span data-ttu-id="01066-333">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="01066-333">Geometry.NumPoints</span></span> | <span data-ttu-id="01066-334">✔</span><span class="sxs-lookup"><span data-stu-id="01066-334">✔</span></span> | <span data-ttu-id="01066-335">✔</span><span class="sxs-lookup"><span data-stu-id="01066-335">✔</span></span> | <span data-ttu-id="01066-336">✔</span><span class="sxs-lookup"><span data-stu-id="01066-336">✔</span></span> | <span data-ttu-id="01066-337">✔</span><span class="sxs-lookup"><span data-stu-id="01066-337">✔</span></span>
<span data-ttu-id="01066-338">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="01066-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="01066-339">✔</span><span class="sxs-lookup"><span data-stu-id="01066-339">✔</span></span> | <span data-ttu-id="01066-340">✔</span><span class="sxs-lookup"><span data-stu-id="01066-340">✔</span></span> | <span data-ttu-id="01066-341">✔</span><span class="sxs-lookup"><span data-stu-id="01066-341">✔</span></span> | <span data-ttu-id="01066-342">✔</span><span class="sxs-lookup"><span data-stu-id="01066-342">✔</span></span>
<span data-ttu-id="01066-343">Geometry. 重なり (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="01066-344">✔</span><span class="sxs-lookup"><span data-stu-id="01066-344">✔</span></span> | <span data-ttu-id="01066-345">✔</span><span class="sxs-lookup"><span data-stu-id="01066-345">✔</span></span> | <span data-ttu-id="01066-346">✔</span><span class="sxs-lookup"><span data-stu-id="01066-346">✔</span></span> | <span data-ttu-id="01066-347">✔</span><span class="sxs-lookup"><span data-stu-id="01066-347">✔</span></span>
<span data-ttu-id="01066-348">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="01066-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="01066-349">✔</span><span class="sxs-lookup"><span data-stu-id="01066-349">✔</span></span> | | <span data-ttu-id="01066-350">✔</span><span class="sxs-lookup"><span data-stu-id="01066-350">✔</span></span> | <span data-ttu-id="01066-351">✔</span><span class="sxs-lookup"><span data-stu-id="01066-351">✔</span></span>
<span data-ttu-id="01066-352">Geometry. 関連付け (Geometry, string)</span><span class="sxs-lookup"><span data-stu-id="01066-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="01066-353">✔</span><span class="sxs-lookup"><span data-stu-id="01066-353">✔</span></span> | | <span data-ttu-id="01066-354">✔</span><span class="sxs-lookup"><span data-stu-id="01066-354">✔</span></span> | <span data-ttu-id="01066-355">✔</span><span class="sxs-lookup"><span data-stu-id="01066-355">✔</span></span>
<span data-ttu-id="01066-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="01066-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="01066-357">✔</span><span class="sxs-lookup"><span data-stu-id="01066-357">✔</span></span> | <span data-ttu-id="01066-358">✔</span><span class="sxs-lookup"><span data-stu-id="01066-358">✔</span></span>
<span data-ttu-id="01066-359">SRID</span><span class="sxs-lookup"><span data-stu-id="01066-359">Geometry.SRID</span></span> | <span data-ttu-id="01066-360">✔</span><span class="sxs-lookup"><span data-stu-id="01066-360">✔</span></span> | <span data-ttu-id="01066-361">✔</span><span class="sxs-lookup"><span data-stu-id="01066-361">✔</span></span> | <span data-ttu-id="01066-362">✔</span><span class="sxs-lookup"><span data-stu-id="01066-362">✔</span></span> | <span data-ttu-id="01066-363">✔</span><span class="sxs-lookup"><span data-stu-id="01066-363">✔</span></span>
<span data-ttu-id="01066-364">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="01066-365">✔</span><span class="sxs-lookup"><span data-stu-id="01066-365">✔</span></span> | <span data-ttu-id="01066-366">✔</span><span class="sxs-lookup"><span data-stu-id="01066-366">✔</span></span> | <span data-ttu-id="01066-367">✔</span><span class="sxs-lookup"><span data-stu-id="01066-367">✔</span></span> | <span data-ttu-id="01066-368">✔</span><span class="sxs-lookup"><span data-stu-id="01066-368">✔</span></span>
<span data-ttu-id="01066-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="01066-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="01066-370">✔</span><span class="sxs-lookup"><span data-stu-id="01066-370">✔</span></span> | <span data-ttu-id="01066-371">✔</span><span class="sxs-lookup"><span data-stu-id="01066-371">✔</span></span> | <span data-ttu-id="01066-372">✔</span><span class="sxs-lookup"><span data-stu-id="01066-372">✔</span></span> | <span data-ttu-id="01066-373">✔</span><span class="sxs-lookup"><span data-stu-id="01066-373">✔</span></span>
<span data-ttu-id="01066-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="01066-374">Geometry.ToText()</span></span> | <span data-ttu-id="01066-375">✔</span><span class="sxs-lookup"><span data-stu-id="01066-375">✔</span></span> | <span data-ttu-id="01066-376">✔</span><span class="sxs-lookup"><span data-stu-id="01066-376">✔</span></span> | <span data-ttu-id="01066-377">✔</span><span class="sxs-lookup"><span data-stu-id="01066-377">✔</span></span> | <span data-ttu-id="01066-378">✔</span><span class="sxs-lookup"><span data-stu-id="01066-378">✔</span></span>
<span data-ttu-id="01066-379">Geometry (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="01066-380">✔</span><span class="sxs-lookup"><span data-stu-id="01066-380">✔</span></span> | | <span data-ttu-id="01066-381">✔</span><span class="sxs-lookup"><span data-stu-id="01066-381">✔</span></span> | <span data-ttu-id="01066-382">✔</span><span class="sxs-lookup"><span data-stu-id="01066-382">✔</span></span>
<span data-ttu-id="01066-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="01066-383">Geometry.Union()</span></span> | | | <span data-ttu-id="01066-384">✔</span><span class="sxs-lookup"><span data-stu-id="01066-384">✔</span></span> | <span data-ttu-id="01066-385">✔</span><span class="sxs-lookup"><span data-stu-id="01066-385">✔</span></span>
<span data-ttu-id="01066-386">Geometry. 共用体 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="01066-387">✔</span><span class="sxs-lookup"><span data-stu-id="01066-387">✔</span></span> | <span data-ttu-id="01066-388">✔</span><span class="sxs-lookup"><span data-stu-id="01066-388">✔</span></span> | <span data-ttu-id="01066-389">✔</span><span class="sxs-lookup"><span data-stu-id="01066-389">✔</span></span> | <span data-ttu-id="01066-390">✔</span><span class="sxs-lookup"><span data-stu-id="01066-390">✔</span></span>
<span data-ttu-id="01066-391">Geometry. 内 (Geometry)</span><span class="sxs-lookup"><span data-stu-id="01066-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="01066-392">✔</span><span class="sxs-lookup"><span data-stu-id="01066-392">✔</span></span> | <span data-ttu-id="01066-393">✔</span><span class="sxs-lookup"><span data-stu-id="01066-393">✔</span></span> | <span data-ttu-id="01066-394">✔</span><span class="sxs-lookup"><span data-stu-id="01066-394">✔</span></span> | <span data-ttu-id="01066-395">✔</span><span class="sxs-lookup"><span data-stu-id="01066-395">✔</span></span>
<span data-ttu-id="01066-396">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="01066-396">GeometryCollection.Count</span></span> | <span data-ttu-id="01066-397">✔</span><span class="sxs-lookup"><span data-stu-id="01066-397">✔</span></span> | <span data-ttu-id="01066-398">✔</span><span class="sxs-lookup"><span data-stu-id="01066-398">✔</span></span> | <span data-ttu-id="01066-399">✔</span><span class="sxs-lookup"><span data-stu-id="01066-399">✔</span></span> | <span data-ttu-id="01066-400">✔</span><span class="sxs-lookup"><span data-stu-id="01066-400">✔</span></span>
<span data-ttu-id="01066-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="01066-401">GeometryCollection[int]</span></span> | <span data-ttu-id="01066-402">✔</span><span class="sxs-lookup"><span data-stu-id="01066-402">✔</span></span> | <span data-ttu-id="01066-403">✔</span><span class="sxs-lookup"><span data-stu-id="01066-403">✔</span></span> | <span data-ttu-id="01066-404">✔</span><span class="sxs-lookup"><span data-stu-id="01066-404">✔</span></span> | <span data-ttu-id="01066-405">✔</span><span class="sxs-lookup"><span data-stu-id="01066-405">✔</span></span>
<span data-ttu-id="01066-406">LineString</span><span class="sxs-lookup"><span data-stu-id="01066-406">LineString.Count</span></span> | <span data-ttu-id="01066-407">✔</span><span class="sxs-lookup"><span data-stu-id="01066-407">✔</span></span> | <span data-ttu-id="01066-408">✔</span><span class="sxs-lookup"><span data-stu-id="01066-408">✔</span></span> | <span data-ttu-id="01066-409">✔</span><span class="sxs-lookup"><span data-stu-id="01066-409">✔</span></span> | <span data-ttu-id="01066-410">✔</span><span class="sxs-lookup"><span data-stu-id="01066-410">✔</span></span>
<span data-ttu-id="01066-411">LineString</span><span class="sxs-lookup"><span data-stu-id="01066-411">LineString.EndPoint</span></span> | <span data-ttu-id="01066-412">✔</span><span class="sxs-lookup"><span data-stu-id="01066-412">✔</span></span> | <span data-ttu-id="01066-413">✔</span><span class="sxs-lookup"><span data-stu-id="01066-413">✔</span></span> | <span data-ttu-id="01066-414">✔</span><span class="sxs-lookup"><span data-stu-id="01066-414">✔</span></span> | <span data-ttu-id="01066-415">✔</span><span class="sxs-lookup"><span data-stu-id="01066-415">✔</span></span>
<span data-ttu-id="01066-416">LineString n (int)</span><span class="sxs-lookup"><span data-stu-id="01066-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="01066-417">✔</span><span class="sxs-lookup"><span data-stu-id="01066-417">✔</span></span> | <span data-ttu-id="01066-418">✔</span><span class="sxs-lookup"><span data-stu-id="01066-418">✔</span></span> | <span data-ttu-id="01066-419">✔</span><span class="sxs-lookup"><span data-stu-id="01066-419">✔</span></span> | <span data-ttu-id="01066-420">✔</span><span class="sxs-lookup"><span data-stu-id="01066-420">✔</span></span>
<span data-ttu-id="01066-421">LineString</span><span class="sxs-lookup"><span data-stu-id="01066-421">LineString.IsClosed</span></span> | <span data-ttu-id="01066-422">✔</span><span class="sxs-lookup"><span data-stu-id="01066-422">✔</span></span> | <span data-ttu-id="01066-423">✔</span><span class="sxs-lookup"><span data-stu-id="01066-423">✔</span></span> | <span data-ttu-id="01066-424">✔</span><span class="sxs-lookup"><span data-stu-id="01066-424">✔</span></span> | <span data-ttu-id="01066-425">✔</span><span class="sxs-lookup"><span data-stu-id="01066-425">✔</span></span>
<span data-ttu-id="01066-426">LineString</span><span class="sxs-lookup"><span data-stu-id="01066-426">LineString.IsRing</span></span> | <span data-ttu-id="01066-427">✔</span><span class="sxs-lookup"><span data-stu-id="01066-427">✔</span></span> | | <span data-ttu-id="01066-428">✔</span><span class="sxs-lookup"><span data-stu-id="01066-428">✔</span></span> | <span data-ttu-id="01066-429">✔</span><span class="sxs-lookup"><span data-stu-id="01066-429">✔</span></span>
<span data-ttu-id="01066-430">LineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="01066-430">LineString.StartPoint</span></span> | <span data-ttu-id="01066-431">✔</span><span class="sxs-lookup"><span data-stu-id="01066-431">✔</span></span> | <span data-ttu-id="01066-432">✔</span><span class="sxs-lookup"><span data-stu-id="01066-432">✔</span></span> | <span data-ttu-id="01066-433">✔</span><span class="sxs-lookup"><span data-stu-id="01066-433">✔</span></span> | <span data-ttu-id="01066-434">✔</span><span class="sxs-lookup"><span data-stu-id="01066-434">✔</span></span>
<span data-ttu-id="01066-435">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="01066-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="01066-436">✔</span><span class="sxs-lookup"><span data-stu-id="01066-436">✔</span></span> | <span data-ttu-id="01066-437">✔</span><span class="sxs-lookup"><span data-stu-id="01066-437">✔</span></span> | <span data-ttu-id="01066-438">✔</span><span class="sxs-lookup"><span data-stu-id="01066-438">✔</span></span> | <span data-ttu-id="01066-439">✔</span><span class="sxs-lookup"><span data-stu-id="01066-439">✔</span></span>
<span data-ttu-id="01066-440">Point. M</span><span class="sxs-lookup"><span data-stu-id="01066-440">Point.M</span></span> | <span data-ttu-id="01066-441">✔</span><span class="sxs-lookup"><span data-stu-id="01066-441">✔</span></span> | <span data-ttu-id="01066-442">✔</span><span class="sxs-lookup"><span data-stu-id="01066-442">✔</span></span> | <span data-ttu-id="01066-443">✔</span><span class="sxs-lookup"><span data-stu-id="01066-443">✔</span></span> | <span data-ttu-id="01066-444">✔</span><span class="sxs-lookup"><span data-stu-id="01066-444">✔</span></span>
<span data-ttu-id="01066-445">Point. X</span><span class="sxs-lookup"><span data-stu-id="01066-445">Point.X</span></span> | <span data-ttu-id="01066-446">✔</span><span class="sxs-lookup"><span data-stu-id="01066-446">✔</span></span> | <span data-ttu-id="01066-447">✔</span><span class="sxs-lookup"><span data-stu-id="01066-447">✔</span></span> | <span data-ttu-id="01066-448">✔</span><span class="sxs-lookup"><span data-stu-id="01066-448">✔</span></span> | <span data-ttu-id="01066-449">✔</span><span class="sxs-lookup"><span data-stu-id="01066-449">✔</span></span>
<span data-ttu-id="01066-450">Point. Y</span><span class="sxs-lookup"><span data-stu-id="01066-450">Point.Y</span></span> | <span data-ttu-id="01066-451">✔</span><span class="sxs-lookup"><span data-stu-id="01066-451">✔</span></span> | <span data-ttu-id="01066-452">✔</span><span class="sxs-lookup"><span data-stu-id="01066-452">✔</span></span> | <span data-ttu-id="01066-453">✔</span><span class="sxs-lookup"><span data-stu-id="01066-453">✔</span></span> | <span data-ttu-id="01066-454">✔</span><span class="sxs-lookup"><span data-stu-id="01066-454">✔</span></span>
<span data-ttu-id="01066-455">Point. Z</span><span class="sxs-lookup"><span data-stu-id="01066-455">Point.Z</span></span> | <span data-ttu-id="01066-456">✔</span><span class="sxs-lookup"><span data-stu-id="01066-456">✔</span></span> | <span data-ttu-id="01066-457">✔</span><span class="sxs-lookup"><span data-stu-id="01066-457">✔</span></span> | <span data-ttu-id="01066-458">✔</span><span class="sxs-lookup"><span data-stu-id="01066-458">✔</span></span> | <span data-ttu-id="01066-459">✔</span><span class="sxs-lookup"><span data-stu-id="01066-459">✔</span></span>
<span data-ttu-id="01066-460">ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="01066-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="01066-461">✔</span><span class="sxs-lookup"><span data-stu-id="01066-461">✔</span></span> | <span data-ttu-id="01066-462">✔</span><span class="sxs-lookup"><span data-stu-id="01066-462">✔</span></span> | <span data-ttu-id="01066-463">✔</span><span class="sxs-lookup"><span data-stu-id="01066-463">✔</span></span> | <span data-ttu-id="01066-464">✔</span><span class="sxs-lookup"><span data-stu-id="01066-464">✔</span></span>
<span data-ttu-id="01066-465">GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="01066-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="01066-466">✔</span><span class="sxs-lookup"><span data-stu-id="01066-466">✔</span></span> | <span data-ttu-id="01066-467">✔</span><span class="sxs-lookup"><span data-stu-id="01066-467">✔</span></span> | <span data-ttu-id="01066-468">✔</span><span class="sxs-lookup"><span data-stu-id="01066-468">✔</span></span> | <span data-ttu-id="01066-469">✔</span><span class="sxs-lookup"><span data-stu-id="01066-469">✔</span></span>
<span data-ttu-id="01066-470">NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="01066-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="01066-471">✔</span><span class="sxs-lookup"><span data-stu-id="01066-471">✔</span></span> | <span data-ttu-id="01066-472">✔</span><span class="sxs-lookup"><span data-stu-id="01066-472">✔</span></span> | <span data-ttu-id="01066-473">✔</span><span class="sxs-lookup"><span data-stu-id="01066-473">✔</span></span> | <span data-ttu-id="01066-474">✔</span><span class="sxs-lookup"><span data-stu-id="01066-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01066-475">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="01066-475">Additional resources</span></span>

* [<span data-ttu-id="01066-476">SQL Server の空間データ</span><span class="sxs-lookup"><span data-stu-id="01066-476">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="01066-477">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="01066-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="01066-478">Npgsql 空間のドキュメント</span><span class="sxs-lookup"><span data-stu-id="01066-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="01066-479">PostGIS のドキュメント</span><span class="sxs-lookup"><span data-stu-id="01066-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)

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
# <a name="spatial-data"></a><span data-ttu-id="0c058-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="0c058-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="0c058-103">この機能は、EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="0c058-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="0c058-104">空間データは、物理的な場所とオブジェクトの形状を表します。</span><span class="sxs-lookup"><span data-stu-id="0c058-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="0c058-105">多くのデータベースは、インデックスを作成し、その他のデータと併せてクエリを実行できるように、この種類のデータのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="0c058-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="0c058-106">一般的なシナリオにはの場所から指定した距離内のオブジェクトの照会や境界には、特定の場所が含まれています。 オブジェクトの選択が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0c058-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="0c058-107">EF Core を使用して空間データ型マッピングをサポートする、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="0c058-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="0c058-108">インストール</span><span class="sxs-lookup"><span data-stu-id="0c058-108">Installing</span></span>

<span data-ttu-id="0c058-109">EF Core の空間データを使用するには、適切なサポートの NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0c058-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="0c058-110">インストールする必要があると、パッケージは、使用しているプロバイダーに依存します。</span><span class="sxs-lookup"><span data-stu-id="0c058-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="0c058-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0c058-111">EF Core Provider</span></span>                        | <span data-ttu-id="0c058-112">空間の NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="0c058-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="0c058-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0c058-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="0c058-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="0c058-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="0c058-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="0c058-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="0c058-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="0c058-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="0c058-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="0c058-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="0c058-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="0c058-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="0c058-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="0c058-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="0c058-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="0c058-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="0c058-121">リバース エンジニア リング</span><span class="sxs-lookup"><span data-stu-id="0c058-121">Reverse engineering</span></span>

<span data-ttu-id="0c058-122">空間の NuGet パッケージのも有効にする[リバース エンジニア リング](../managing-schemas/scaffolding.md)が空間のプロパティを使用したモデルは、パッケージをインストールする必要があります***する前に***を実行している`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`します。</span><span class="sxs-lookup"><span data-stu-id="0c058-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="0c058-123">ない場合は、列の型のマッピングを検出していないに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="0c058-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="0c058-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="0c058-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="0c058-125">NetTopologySuite は、.NET の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="0c058-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="0c058-126">EF Core では、モデルの NTS 型を使用して、空間データをデータベースで型のマッピングを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0c058-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="0c058-127">NTS 経由での空間型へのマッピングを有効にするには、プロバイダーの DbContext オプション ビルダーを UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0c058-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="0c058-128">たとえば、SQL Server で、次のように呼び出すことは。</span><span class="sxs-lookup"><span data-stu-id="0c058-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="0c058-129">いくつかの空間データの種類があります。</span><span class="sxs-lookup"><span data-stu-id="0c058-129">There are several spatial data types.</span></span> <span data-ttu-id="0c058-130">使用する種類を許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="0c058-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="0c058-131">モデルのプロパティを使用できる NTS 型の階層を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0c058-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="0c058-132">内にある、`NetTopologySuite.Geometries`名前空間。</span><span class="sxs-lookup"><span data-stu-id="0c058-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="0c058-133">GeoAPI パッケージ内の対応するインターフェイス (`GeoAPI.Geometries`名前空間) こともできます。</span><span class="sxs-lookup"><span data-stu-id="0c058-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="0c058-134">geometry</span><span class="sxs-lookup"><span data-stu-id="0c058-134">Geometry</span></span>
  * <span data-ttu-id="0c058-135">ポイント</span><span class="sxs-lookup"><span data-stu-id="0c058-135">Point</span></span>
  * <span data-ttu-id="0c058-136">LineString</span><span class="sxs-lookup"><span data-stu-id="0c058-136">LineString</span></span>
  * <span data-ttu-id="0c058-137">多角形</span><span class="sxs-lookup"><span data-stu-id="0c058-137">Polygon</span></span>
  * <span data-ttu-id="0c058-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="0c058-138">GeometryCollection</span></span>
    * <span data-ttu-id="0c058-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="0c058-139">MultiPoint</span></span>
    * <span data-ttu-id="0c058-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="0c058-140">MultiLineString</span></span>
    * <span data-ttu-id="0c058-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="0c058-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="0c058-142">CircularString、CompoundCurve、および CurePolygon は NTS によってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0c058-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="0c058-143">基本の Geometry 型を使用すると、どの種類のプロパティで指定する図形ができます。</span><span class="sxs-lookup"><span data-stu-id="0c058-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="0c058-144">次のエンティティ クラスを使用して、内のテーブルにマップする可能性があります、 [Wide World Importers サンプル データベース](http://go.microsoft.com/fwlink/?LinkID=800630)します。</span><span class="sxs-lookup"><span data-stu-id="0c058-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="0c058-145">値を作成します。</span><span class="sxs-lookup"><span data-stu-id="0c058-145">Creating values</span></span>

<span data-ttu-id="0c058-146">Geometry オブジェクトを作成するのにコンス トラクターを使用できます。ただし、NTS では、geometry ファクトリを代わりに使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0c058-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="0c058-147">これは、既定の SRID (使用する、座標空間参照系) を指定することができ、できます (計算中に使用)、有効桁数のモデルと (どの座標--ディメンションを決定する座標のシーケンスなどのより高度な制御メジャーを利用)。</span><span class="sxs-lookup"><span data-stu-id="0c058-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="0c058-148">4326 は、wgs-84、GPS およびその他の地理的なシステムで使用される標準を参照します。</span><span class="sxs-lookup"><span data-stu-id="0c058-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="0c058-149">緯度と経度</span><span class="sxs-lookup"><span data-stu-id="0c058-149">Longitude and Latitude</span></span>

<span data-ttu-id="0c058-150">NTS で座標がの観点では X と Y の値。</span><span class="sxs-lookup"><span data-stu-id="0c058-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="0c058-151">経度と緯度を表現するには、緯度経度と Y の X を使用します。</span><span class="sxs-lookup"><span data-stu-id="0c058-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="0c058-152">これは**下位**から、`latitude, longitude`形式は通常、これらの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="0c058-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="0c058-153">クライアントの操作中に SRID が無視されます。</span><span class="sxs-lookup"><span data-stu-id="0c058-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="0c058-154">NTS は、操作中に、SRID 値を無視します。</span><span class="sxs-lookup"><span data-stu-id="0c058-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="0c058-155">平面座標系が想定しています。</span><span class="sxs-lookup"><span data-stu-id="0c058-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="0c058-156">つまり、経度と緯度、距離、長さ、および領域がないメーターの角度でになりますようないくつかのクライアントで評価される値の観点からの座標を指定する場合。</span><span class="sxs-lookup"><span data-stu-id="0c058-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="0c058-157">意味のある値は、まずにプロジェクトの座標などのライブラリを使用して、もう 1 つの座標系を[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)これらの値を計算する前にします。</span><span class="sxs-lookup"><span data-stu-id="0c058-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="0c058-158">操作が SQL 経由での EF Core によってサーバーで評価される場合は、結果のユニットは、データベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="0c058-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="0c058-159">ProjNet4GeoAPI を使用して、2 つの都市間の距離を計算する例を示します。</span><span class="sxs-lookup"><span data-stu-id="0c058-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="0c058-160">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="0c058-160">Querying Data</span></span>

<span data-ttu-id="0c058-161">LINQ では、NTS メソッドとデータベース機能として使用できるプロパティは SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0c058-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="0c058-162">たとえば、距離と Contains メソッドは、次のクエリに変換されます。</span><span class="sxs-lookup"><span data-stu-id="0c058-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="0c058-163">この記事の最後にある表では、さまざまな EF Core プロバイダーによってサポートされるメンバーを示します。</span><span class="sxs-lookup"><span data-stu-id="0c058-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="0c058-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="0c058-164">SQL Server</span></span>

<span data-ttu-id="0c058-165">SQL Server を使用している場合の注意すべき点があります。</span><span class="sxs-lookup"><span data-stu-id="0c058-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="0c058-166">地理またはジオメトリ</span><span class="sxs-lookup"><span data-stu-id="0c058-166">Geography or geometry</span></span>

<span data-ttu-id="0c058-167">既定では、空間プロパティのマップ`geography`SQL Server 内の列。</span><span class="sxs-lookup"><span data-stu-id="0c058-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="0c058-168">使用する`geometry`、[列の型を構成する](xref:core/modeling/relational/data-types)モデル。</span><span class="sxs-lookup"><span data-stu-id="0c058-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="0c058-169">地理の多角形のリング</span><span class="sxs-lookup"><span data-stu-id="0c058-169">Geography polygon rings</span></span>

<span data-ttu-id="0c058-170">使用する場合、`geography`列型、SQL Server が外部リング (またはシェル) に追加の要件を課すと内部リング (穴)。</span><span class="sxs-lookup"><span data-stu-id="0c058-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="0c058-171">外部リング指向左回りには、内部リングを時計回りにします。</span><span class="sxs-lookup"><span data-stu-id="0c058-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="0c058-172">NTS は、値をデータベースに送信する前に、これを検証します。</span><span class="sxs-lookup"><span data-stu-id="0c058-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="0c058-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="0c058-173">FullGlobe</span></span>

<span data-ttu-id="0c058-174">SQL Server が非標準の geometry 型を使用する場合は、全球を表す、`geography`列の型。</span><span class="sxs-lookup"><span data-stu-id="0c058-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="0c058-175">全球 (せずに、外部リング) に基づく多角形を表す方法もあります。</span><span class="sxs-lookup"><span data-stu-id="0c058-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="0c058-176">どちらもは NTS でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0c058-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="0c058-177">FullGlobe とそれに基づく多角形は、NTS でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0c058-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="0c058-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="0c058-178">SQLite</span></span>

<span data-ttu-id="0c058-179">SQLite を使用する場合のいくつか追加の情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0c058-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="0c058-180">SpatiaLite をインストールします。</span><span class="sxs-lookup"><span data-stu-id="0c058-180">Installing SpatiaLite</span></span>

<span data-ttu-id="0c058-181">、Windows でネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="0c058-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="0c058-182">その他のプラットフォームでは、別途インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0c058-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="0c058-183">これは、通常のソフトウェアのパッケージ マネージャーを使用します。</span><span class="sxs-lookup"><span data-stu-id="0c058-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="0c058-184">たとえば、Ubuntu または macos の Homebrew での APT を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0c058-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="0c058-185">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="0c058-185">Configuring SRID</span></span>

<span data-ttu-id="0c058-186">SpatiaLite、列を 1 列あたり SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0c058-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="0c058-187">既定の SRID は`0`します。</span><span class="sxs-lookup"><span data-stu-id="0c058-187">The default SRID is `0`.</span></span> <span data-ttu-id="0c058-188">ForSqliteHasSrid メソッドを使用して異なる SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="0c058-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="0c058-189">ディメンション</span><span class="sxs-lookup"><span data-stu-id="0c058-189">Dimension</span></span>

<span data-ttu-id="0c058-190">SRID と同様に、列のディメンション (または座標) がも指定されて、列の一部として。</span><span class="sxs-lookup"><span data-stu-id="0c058-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="0c058-191">既定の座標は X と適用を有効にする追加の座標 (z 座標および M) が ForSqliteHasDimension メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="0c058-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="0c058-192">翻訳済みの操作</span><span class="sxs-lookup"><span data-stu-id="0c058-192">Translated Operations</span></span>

<span data-ttu-id="0c058-193">次の表では、どの NTS メンバーは、EF Core プロバイダーによって SQL に変換を示します。</span><span class="sxs-lookup"><span data-stu-id="0c058-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="0c058-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="0c058-194">NetTopologySuite</span></span> | <span data-ttu-id="0c058-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-195">SQL Server (geometry)</span></span> | <span data-ttu-id="0c058-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="0c058-196">SQL Server (geography)</span></span> | <span data-ttu-id="0c058-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="0c058-197">SQLite</span></span> | <span data-ttu-id="0c058-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="0c058-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="0c058-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="0c058-199">Geometry.Area</span></span> | <span data-ttu-id="0c058-200">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-200">✔</span></span> | <span data-ttu-id="0c058-201">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-201">✔</span></span> | <span data-ttu-id="0c058-202">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-202">✔</span></span> | <span data-ttu-id="0c058-203">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-203">✔</span></span>
<span data-ttu-id="0c058-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="0c058-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="0c058-205">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-205">✔</span></span> | <span data-ttu-id="0c058-206">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-206">✔</span></span> | <span data-ttu-id="0c058-207">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-207">✔</span></span> | <span data-ttu-id="0c058-208">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-208">✔</span></span>
<span data-ttu-id="0c058-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="0c058-209">Geometry.AsText()</span></span> | <span data-ttu-id="0c058-210">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-210">✔</span></span> | <span data-ttu-id="0c058-211">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-211">✔</span></span> | <span data-ttu-id="0c058-212">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-212">✔</span></span> | <span data-ttu-id="0c058-213">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-213">✔</span></span>
<span data-ttu-id="0c058-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="0c058-214">Geometry.Boundary</span></span> | <span data-ttu-id="0c058-215">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-215">✔</span></span> | | <span data-ttu-id="0c058-216">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-216">✔</span></span> | <span data-ttu-id="0c058-217">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-217">✔</span></span>
<span data-ttu-id="0c058-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="0c058-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="0c058-219">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-219">✔</span></span> | <span data-ttu-id="0c058-220">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-220">✔</span></span> | <span data-ttu-id="0c058-221">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-221">✔</span></span> | <span data-ttu-id="0c058-222">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-222">✔</span></span>
<span data-ttu-id="0c058-223">Geometry.Buffer (int、double)</span><span class="sxs-lookup"><span data-stu-id="0c058-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="0c058-224">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-224">✔</span></span>
<span data-ttu-id="0c058-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="0c058-225">Geometry.Centroid</span></span> | <span data-ttu-id="0c058-226">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-226">✔</span></span> | | <span data-ttu-id="0c058-227">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-227">✔</span></span> | <span data-ttu-id="0c058-228">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-228">✔</span></span>
<span data-ttu-id="0c058-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="0c058-230">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-230">✔</span></span> | <span data-ttu-id="0c058-231">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-231">✔</span></span> | <span data-ttu-id="0c058-232">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-232">✔</span></span> | <span data-ttu-id="0c058-233">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-233">✔</span></span>
<span data-ttu-id="0c058-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="0c058-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="0c058-235">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-235">✔</span></span> | <span data-ttu-id="0c058-236">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-236">✔</span></span> | <span data-ttu-id="0c058-237">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-237">✔</span></span> | <span data-ttu-id="0c058-238">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-238">✔</span></span>
<span data-ttu-id="0c058-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="0c058-240">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-240">✔</span></span> | <span data-ttu-id="0c058-241">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-241">✔</span></span>
<span data-ttu-id="0c058-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="0c058-243">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-243">✔</span></span> | <span data-ttu-id="0c058-244">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-244">✔</span></span>
<span data-ttu-id="0c058-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="0c058-246">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-246">✔</span></span> | | <span data-ttu-id="0c058-247">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-247">✔</span></span> | <span data-ttu-id="0c058-248">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-248">✔</span></span>
<span data-ttu-id="0c058-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="0c058-250">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-250">✔</span></span> | <span data-ttu-id="0c058-251">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-251">✔</span></span> | <span data-ttu-id="0c058-252">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-252">✔</span></span> | <span data-ttu-id="0c058-253">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-253">✔</span></span>
<span data-ttu-id="0c058-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="0c058-254">Geometry.Dimension</span></span> | <span data-ttu-id="0c058-255">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-255">✔</span></span> | <span data-ttu-id="0c058-256">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-256">✔</span></span> | <span data-ttu-id="0c058-257">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-257">✔</span></span> | <span data-ttu-id="0c058-258">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-258">✔</span></span>
<span data-ttu-id="0c058-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="0c058-260">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-260">✔</span></span> | <span data-ttu-id="0c058-261">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-261">✔</span></span> | <span data-ttu-id="0c058-262">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-262">✔</span></span> | <span data-ttu-id="0c058-263">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-263">✔</span></span>
<span data-ttu-id="0c058-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="0c058-265">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-265">✔</span></span> | <span data-ttu-id="0c058-266">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-266">✔</span></span> | <span data-ttu-id="0c058-267">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-267">✔</span></span> | <span data-ttu-id="0c058-268">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-268">✔</span></span>
<span data-ttu-id="0c058-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="0c058-269">Geometry.Envelope</span></span> | <span data-ttu-id="0c058-270">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-270">✔</span></span> | | <span data-ttu-id="0c058-271">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-271">✔</span></span> | <span data-ttu-id="0c058-272">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-272">✔</span></span>
<span data-ttu-id="0c058-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="0c058-274">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-274">✔</span></span>
<span data-ttu-id="0c058-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="0c058-276">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-276">✔</span></span> | <span data-ttu-id="0c058-277">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-277">✔</span></span> | <span data-ttu-id="0c058-278">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-278">✔</span></span> | <span data-ttu-id="0c058-279">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-279">✔</span></span>
<span data-ttu-id="0c058-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="0c058-280">Geometry.GeometryType</span></span> | <span data-ttu-id="0c058-281">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-281">✔</span></span> | <span data-ttu-id="0c058-282">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-282">✔</span></span> | <span data-ttu-id="0c058-283">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-283">✔</span></span> | <span data-ttu-id="0c058-284">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-284">✔</span></span>
<span data-ttu-id="0c058-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="0c058-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="0c058-286">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-286">✔</span></span> | | <span data-ttu-id="0c058-287">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-287">✔</span></span> | <span data-ttu-id="0c058-288">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-288">✔</span></span>
<span data-ttu-id="0c058-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="0c058-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="0c058-290">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-290">✔</span></span> | | <span data-ttu-id="0c058-291">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-291">✔</span></span>
<span data-ttu-id="0c058-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="0c058-293">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-293">✔</span></span> | <span data-ttu-id="0c058-294">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-294">✔</span></span> | <span data-ttu-id="0c058-295">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-295">✔</span></span> | <span data-ttu-id="0c058-296">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-296">✔</span></span>
<span data-ttu-id="0c058-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="0c058-298">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-298">✔</span></span> | <span data-ttu-id="0c058-299">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-299">✔</span></span> | <span data-ttu-id="0c058-300">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-300">✔</span></span> | <span data-ttu-id="0c058-301">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-301">✔</span></span>
<span data-ttu-id="0c058-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="0c058-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="0c058-303">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-303">✔</span></span> | <span data-ttu-id="0c058-304">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-304">✔</span></span> | <span data-ttu-id="0c058-305">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-305">✔</span></span> | <span data-ttu-id="0c058-306">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-306">✔</span></span>
<span data-ttu-id="0c058-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="0c058-307">Geometry.IsSimple</span></span> | <span data-ttu-id="0c058-308">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-308">✔</span></span> | | <span data-ttu-id="0c058-309">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-309">✔</span></span> | <span data-ttu-id="0c058-310">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-310">✔</span></span>
<span data-ttu-id="0c058-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="0c058-311">Geometry.IsValid</span></span> | <span data-ttu-id="0c058-312">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-312">✔</span></span> | <span data-ttu-id="0c058-313">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-313">✔</span></span> | <span data-ttu-id="0c058-314">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-314">✔</span></span> | <span data-ttu-id="0c058-315">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-315">✔</span></span>
<span data-ttu-id="0c058-316">Geometry.IsWithinDistance (Geometry、double)</span><span class="sxs-lookup"><span data-stu-id="0c058-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="0c058-317">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-317">✔</span></span> | | <span data-ttu-id="0c058-318">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-318">✔</span></span>
<span data-ttu-id="0c058-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="0c058-319">Geometry.Length</span></span> | <span data-ttu-id="0c058-320">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-320">✔</span></span> | <span data-ttu-id="0c058-321">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-321">✔</span></span> | <span data-ttu-id="0c058-322">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-322">✔</span></span> | <span data-ttu-id="0c058-323">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-323">✔</span></span>
<span data-ttu-id="0c058-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="0c058-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="0c058-325">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-325">✔</span></span> | <span data-ttu-id="0c058-326">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-326">✔</span></span> | <span data-ttu-id="0c058-327">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-327">✔</span></span> | <span data-ttu-id="0c058-328">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-328">✔</span></span>
<span data-ttu-id="0c058-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="0c058-329">Geometry.NumPoints</span></span> | <span data-ttu-id="0c058-330">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-330">✔</span></span> | <span data-ttu-id="0c058-331">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-331">✔</span></span> | <span data-ttu-id="0c058-332">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-332">✔</span></span> | <span data-ttu-id="0c058-333">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-333">✔</span></span>
<span data-ttu-id="0c058-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="0c058-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="0c058-335">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-335">✔</span></span> | <span data-ttu-id="0c058-336">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-336">✔</span></span> | <span data-ttu-id="0c058-337">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-337">✔</span></span>
<span data-ttu-id="0c058-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="0c058-339">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-339">✔</span></span> | <span data-ttu-id="0c058-340">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-340">✔</span></span> | <span data-ttu-id="0c058-341">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-341">✔</span></span> | <span data-ttu-id="0c058-342">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-342">✔</span></span>
<span data-ttu-id="0c058-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="0c058-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="0c058-344">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-344">✔</span></span> | | <span data-ttu-id="0c058-345">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-345">✔</span></span> | <span data-ttu-id="0c058-346">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-346">✔</span></span>
<span data-ttu-id="0c058-347">Geometry.Relate (Geometry、文字列)</span><span class="sxs-lookup"><span data-stu-id="0c058-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="0c058-348">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-348">✔</span></span> | | <span data-ttu-id="0c058-349">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-349">✔</span></span> | <span data-ttu-id="0c058-350">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-350">✔</span></span>
<span data-ttu-id="0c058-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="0c058-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="0c058-352">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-352">✔</span></span> | <span data-ttu-id="0c058-353">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-353">✔</span></span>
<span data-ttu-id="0c058-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="0c058-354">Geometry.SRID</span></span> | <span data-ttu-id="0c058-355">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-355">✔</span></span> | <span data-ttu-id="0c058-356">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-356">✔</span></span> | <span data-ttu-id="0c058-357">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-357">✔</span></span> | <span data-ttu-id="0c058-358">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-358">✔</span></span>
<span data-ttu-id="0c058-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="0c058-360">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-360">✔</span></span> | <span data-ttu-id="0c058-361">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-361">✔</span></span> | <span data-ttu-id="0c058-362">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-362">✔</span></span> | <span data-ttu-id="0c058-363">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-363">✔</span></span>
<span data-ttu-id="0c058-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="0c058-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="0c058-365">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-365">✔</span></span> | <span data-ttu-id="0c058-366">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-366">✔</span></span> | <span data-ttu-id="0c058-367">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-367">✔</span></span> | <span data-ttu-id="0c058-368">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-368">✔</span></span>
<span data-ttu-id="0c058-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="0c058-369">Geometry.ToText()</span></span> | <span data-ttu-id="0c058-370">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-370">✔</span></span> | <span data-ttu-id="0c058-371">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-371">✔</span></span> | <span data-ttu-id="0c058-372">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-372">✔</span></span> | <span data-ttu-id="0c058-373">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-373">✔</span></span>
<span data-ttu-id="0c058-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="0c058-375">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-375">✔</span></span> | | <span data-ttu-id="0c058-376">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-376">✔</span></span> | <span data-ttu-id="0c058-377">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-377">✔</span></span>
<span data-ttu-id="0c058-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="0c058-378">Geometry.Union()</span></span> | | | <span data-ttu-id="0c058-379">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-379">✔</span></span>
<span data-ttu-id="0c058-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="0c058-381">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-381">✔</span></span> | <span data-ttu-id="0c058-382">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-382">✔</span></span> | <span data-ttu-id="0c058-383">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-383">✔</span></span> | <span data-ttu-id="0c058-384">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-384">✔</span></span>
<span data-ttu-id="0c058-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="0c058-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="0c058-386">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-386">✔</span></span> | <span data-ttu-id="0c058-387">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-387">✔</span></span> | <span data-ttu-id="0c058-388">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-388">✔</span></span> | <span data-ttu-id="0c058-389">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-389">✔</span></span>
<span data-ttu-id="0c058-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="0c058-390">GeometryCollection.Count</span></span> | <span data-ttu-id="0c058-391">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-391">✔</span></span> | <span data-ttu-id="0c058-392">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-392">✔</span></span> | <span data-ttu-id="0c058-393">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-393">✔</span></span> | <span data-ttu-id="0c058-394">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-394">✔</span></span>
<span data-ttu-id="0c058-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="0c058-395">GeometryCollection[int]</span></span> | <span data-ttu-id="0c058-396">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-396">✔</span></span> | <span data-ttu-id="0c058-397">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-397">✔</span></span> | <span data-ttu-id="0c058-398">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-398">✔</span></span> | <span data-ttu-id="0c058-399">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-399">✔</span></span>
<span data-ttu-id="0c058-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="0c058-400">LineString.Count</span></span> | <span data-ttu-id="0c058-401">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-401">✔</span></span> | <span data-ttu-id="0c058-402">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-402">✔</span></span> | <span data-ttu-id="0c058-403">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-403">✔</span></span> | <span data-ttu-id="0c058-404">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-404">✔</span></span>
<span data-ttu-id="0c058-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="0c058-405">LineString.EndPoint</span></span> | <span data-ttu-id="0c058-406">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-406">✔</span></span> | <span data-ttu-id="0c058-407">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-407">✔</span></span> | <span data-ttu-id="0c058-408">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-408">✔</span></span> | <span data-ttu-id="0c058-409">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-409">✔</span></span>
<span data-ttu-id="0c058-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="0c058-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="0c058-411">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-411">✔</span></span> | <span data-ttu-id="0c058-412">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-412">✔</span></span> | <span data-ttu-id="0c058-413">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-413">✔</span></span> | <span data-ttu-id="0c058-414">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-414">✔</span></span>
<span data-ttu-id="0c058-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="0c058-415">LineString.IsClosed</span></span> | <span data-ttu-id="0c058-416">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-416">✔</span></span> | <span data-ttu-id="0c058-417">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-417">✔</span></span> | <span data-ttu-id="0c058-418">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-418">✔</span></span> | <span data-ttu-id="0c058-419">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-419">✔</span></span>
<span data-ttu-id="0c058-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="0c058-420">LineString.IsRing</span></span> | <span data-ttu-id="0c058-421">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-421">✔</span></span> | | <span data-ttu-id="0c058-422">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-422">✔</span></span> | <span data-ttu-id="0c058-423">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-423">✔</span></span>
<span data-ttu-id="0c058-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="0c058-424">LineString.StartPoint</span></span> | <span data-ttu-id="0c058-425">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-425">✔</span></span> | <span data-ttu-id="0c058-426">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-426">✔</span></span> | <span data-ttu-id="0c058-427">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-427">✔</span></span> | <span data-ttu-id="0c058-428">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-428">✔</span></span>
<span data-ttu-id="0c058-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="0c058-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="0c058-430">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-430">✔</span></span> | <span data-ttu-id="0c058-431">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-431">✔</span></span> | <span data-ttu-id="0c058-432">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-432">✔</span></span> | <span data-ttu-id="0c058-433">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-433">✔</span></span>
<span data-ttu-id="0c058-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="0c058-434">Point.M</span></span> | <span data-ttu-id="0c058-435">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-435">✔</span></span> | <span data-ttu-id="0c058-436">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-436">✔</span></span> | <span data-ttu-id="0c058-437">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-437">✔</span></span> | <span data-ttu-id="0c058-438">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-438">✔</span></span>
<span data-ttu-id="0c058-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="0c058-439">Point.X</span></span> | <span data-ttu-id="0c058-440">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-440">✔</span></span> | <span data-ttu-id="0c058-441">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-441">✔</span></span> | <span data-ttu-id="0c058-442">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-442">✔</span></span> | <span data-ttu-id="0c058-443">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-443">✔</span></span>
<span data-ttu-id="0c058-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="0c058-444">Point.Y</span></span> | <span data-ttu-id="0c058-445">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-445">✔</span></span> | <span data-ttu-id="0c058-446">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-446">✔</span></span> | <span data-ttu-id="0c058-447">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-447">✔</span></span> | <span data-ttu-id="0c058-448">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-448">✔</span></span>
<span data-ttu-id="0c058-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="0c058-449">Point.Z</span></span> | <span data-ttu-id="0c058-450">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-450">✔</span></span> | <span data-ttu-id="0c058-451">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-451">✔</span></span> | <span data-ttu-id="0c058-452">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-452">✔</span></span> | <span data-ttu-id="0c058-453">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-453">✔</span></span>
<span data-ttu-id="0c058-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="0c058-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="0c058-455">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-455">✔</span></span> | <span data-ttu-id="0c058-456">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-456">✔</span></span> | <span data-ttu-id="0c058-457">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-457">✔</span></span> | <span data-ttu-id="0c058-458">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-458">✔</span></span>
<span data-ttu-id="0c058-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="0c058-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="0c058-460">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-460">✔</span></span> | <span data-ttu-id="0c058-461">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-461">✔</span></span> | <span data-ttu-id="0c058-462">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-462">✔</span></span> | <span data-ttu-id="0c058-463">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-463">✔</span></span>
<span data-ttu-id="0c058-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="0c058-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="0c058-465">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-465">✔</span></span> | <span data-ttu-id="0c058-466">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-466">✔</span></span> | <span data-ttu-id="0c058-467">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-467">✔</span></span> | <span data-ttu-id="0c058-468">✔</span><span class="sxs-lookup"><span data-stu-id="0c058-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c058-469">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0c058-469">Additional resources</span></span>

* [<span data-ttu-id="0c058-470">SQL Server での空間データ</span><span class="sxs-lookup"><span data-stu-id="0c058-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="0c058-471">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="0c058-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="0c058-472">Npgsql 空間ドキュメント</span><span class="sxs-lookup"><span data-stu-id="0c058-472">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="0c058-473">PostGIS ドキュメント</span><span class="sxs-lookup"><span data-stu-id="0c058-473">PostGIS Documentation</span></span>](http://postgis.net/documentation/)

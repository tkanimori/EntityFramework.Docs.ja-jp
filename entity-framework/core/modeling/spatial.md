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
# <a name="spatial-data"></a><span data-ttu-id="eda18-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="eda18-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="eda18-103">この機能は、EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="eda18-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="eda18-104">空間データは、物理的な場所とオブジェクトの形状を表します。</span><span class="sxs-lookup"><span data-stu-id="eda18-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="eda18-105">多くのデータベースは、インデックスを作成し、その他のデータと併せてクエリを実行できるように、この種類のデータのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="eda18-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="eda18-106">一般的なシナリオにはの場所から指定した距離内のオブジェクトの照会や境界には、特定の場所が含まれています。 オブジェクトの選択が含まれます。</span><span class="sxs-lookup"><span data-stu-id="eda18-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="eda18-107">EF Core を使用して空間データ型マッピングをサポートする、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="eda18-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="eda18-108">インストール</span><span class="sxs-lookup"><span data-stu-id="eda18-108">Installing</span></span>

<span data-ttu-id="eda18-109">EF Core の空間データを使用するには、適切なサポートの NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eda18-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="eda18-110">インストールする必要があると、パッケージは、使用しているプロバイダーに依存します。</span><span class="sxs-lookup"><span data-stu-id="eda18-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="eda18-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="eda18-111">EF Core Provider</span></span>                        | <span data-ttu-id="eda18-112">空間の NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="eda18-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="eda18-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="eda18-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="eda18-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="eda18-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="eda18-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="eda18-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="eda18-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="eda18-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="eda18-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="eda18-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="eda18-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="eda18-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="eda18-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="eda18-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="eda18-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="eda18-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="eda18-121">リバース エンジニア リング</span><span class="sxs-lookup"><span data-stu-id="eda18-121">Reverse engineering</span></span>

<span data-ttu-id="eda18-122">空間の NuGet パッケージのも有効にする[リバース エンジニア リング](../managing-schemas/scaffolding.md)が空間のプロパティを使用したモデルは、パッケージをインストールする必要があります***する前に***を実行している`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`します。</span><span class="sxs-lookup"><span data-stu-id="eda18-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="eda18-123">ない場合は、列の型のマッピングを検出していないに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="eda18-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="eda18-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="eda18-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="eda18-125">NetTopologySuite は、.NET の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="eda18-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="eda18-126">EF Core では、モデルの NTS 型を使用して、空間データをデータベースで型のマッピングを使用できます。</span><span class="sxs-lookup"><span data-stu-id="eda18-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="eda18-127">NTS 経由での空間型へのマッピングを有効にするには、プロバイダーの DbContext オプション ビルダーを UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eda18-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="eda18-128">たとえば、SQL Server で、次のように呼び出すことは。</span><span class="sxs-lookup"><span data-stu-id="eda18-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="eda18-129">いくつかの空間データの種類があります。</span><span class="sxs-lookup"><span data-stu-id="eda18-129">There are several spatial data types.</span></span> <span data-ttu-id="eda18-130">使用する種類を許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="eda18-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="eda18-131">モデルのプロパティを使用できる NTS 型の階層を次に示します。</span><span class="sxs-lookup"><span data-stu-id="eda18-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="eda18-132">内にある、`NetTopologySuite.Geometries`名前空間。</span><span class="sxs-lookup"><span data-stu-id="eda18-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="eda18-133">GeoAPI パッケージ内の対応するインターフェイス (`GeoAPI.Geometries`名前空間) こともできます。</span><span class="sxs-lookup"><span data-stu-id="eda18-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="eda18-134">geometry</span><span class="sxs-lookup"><span data-stu-id="eda18-134">Geometry</span></span>
  * <span data-ttu-id="eda18-135">ポイント</span><span class="sxs-lookup"><span data-stu-id="eda18-135">Point</span></span>
  * <span data-ttu-id="eda18-136">LineString</span><span class="sxs-lookup"><span data-stu-id="eda18-136">LineString</span></span>
  * <span data-ttu-id="eda18-137">多角形</span><span class="sxs-lookup"><span data-stu-id="eda18-137">Polygon</span></span>
  * <span data-ttu-id="eda18-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="eda18-138">GeometryCollection</span></span>
    * <span data-ttu-id="eda18-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="eda18-139">MultiPoint</span></span>
    * <span data-ttu-id="eda18-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="eda18-140">MultiLineString</span></span>
    * <span data-ttu-id="eda18-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="eda18-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="eda18-142">CircularString、CompoundCurve、および CurePolygon は NTS によってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="eda18-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="eda18-143">基本の Geometry 型を使用すると、どの種類のプロパティで指定する図形ができます。</span><span class="sxs-lookup"><span data-stu-id="eda18-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="eda18-144">次のエンティティ クラスを使用して、内のテーブルにマップする可能性があります、 [Wide World Importers サンプル データベース](http://go.microsoft.com/fwlink/?LinkID=800630)します。</span><span class="sxs-lookup"><span data-stu-id="eda18-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="eda18-145">値を作成します。</span><span class="sxs-lookup"><span data-stu-id="eda18-145">Creating values</span></span>

<span data-ttu-id="eda18-146">Geometry オブジェクトを作成するのにコンストラクターを使用できます。ただし、NTS では、geometry ファクトリを代わりに使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="eda18-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="eda18-147">これは、既定の SRID (使用する、座標空間参照系) を指定することができ、できます (計算中に使用)、有効桁数のモデルと (どの座標--ディメンションを決定する座標のシーケンスなどのより高度な制御メジャーを利用)。</span><span class="sxs-lookup"><span data-stu-id="eda18-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="eda18-148">4326 は、wgs-84、GPS およびその他の地理的なシステムで使用される標準を参照します。</span><span class="sxs-lookup"><span data-stu-id="eda18-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="eda18-149">緯度と経度</span><span class="sxs-lookup"><span data-stu-id="eda18-149">Longitude and Latitude</span></span>

<span data-ttu-id="eda18-150">NTS で座標がの観点では X と Y の値。</span><span class="sxs-lookup"><span data-stu-id="eda18-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="eda18-151">経度と緯度を表現するには、緯度経度と Y の X を使用します。</span><span class="sxs-lookup"><span data-stu-id="eda18-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="eda18-152">これは**下位**から、`latitude, longitude`形式は通常、これらの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="eda18-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="eda18-153">クライアントの操作中に SRID が無視されます。</span><span class="sxs-lookup"><span data-stu-id="eda18-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="eda18-154">NTS は、操作中に、SRID 値を無視します。</span><span class="sxs-lookup"><span data-stu-id="eda18-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="eda18-155">平面座標系が想定しています。</span><span class="sxs-lookup"><span data-stu-id="eda18-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="eda18-156">つまり、経度と緯度、距離、長さ、および領域がないメーターの角度でになりますようないくつかのクライアントで評価される値の観点からの座標を指定する場合。</span><span class="sxs-lookup"><span data-stu-id="eda18-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="eda18-157">意味のある値は、まずにプロジェクトの座標などのライブラリを使用して、もう 1 つの座標系を[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)これらの値を計算する前にします。</span><span class="sxs-lookup"><span data-stu-id="eda18-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="eda18-158">操作が SQL 経由での EF Core によってサーバーで評価される場合は、結果のユニットは、データベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="eda18-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="eda18-159">ProjNet4GeoAPI を使用して、2 つの都市間の距離を計算する例を示します。</span><span class="sxs-lookup"><span data-stu-id="eda18-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="eda18-160">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="eda18-160">Querying Data</span></span>

<span data-ttu-id="eda18-161">LINQ では、NTS メソッドとデータベース機能として使用できるプロパティは SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="eda18-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="eda18-162">たとえば、距離と Contains メソッドは、次のクエリに変換されます。</span><span class="sxs-lookup"><span data-stu-id="eda18-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="eda18-163">この記事の最後にある表では、さまざまな EF Core プロバイダーによってサポートされるメンバーを示します。</span><span class="sxs-lookup"><span data-stu-id="eda18-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="eda18-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="eda18-164">SQL Server</span></span>

<span data-ttu-id="eda18-165">SQL Server を使用している場合の注意すべき点があります。</span><span class="sxs-lookup"><span data-stu-id="eda18-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="eda18-166">地理またはジオメトリ</span><span class="sxs-lookup"><span data-stu-id="eda18-166">Geography or geometry</span></span>

<span data-ttu-id="eda18-167">既定では、空間プロパティのマップ`geography`SQL Server 内の列。</span><span class="sxs-lookup"><span data-stu-id="eda18-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="eda18-168">使用する`geometry`、[列の型を構成する](xref:core/modeling/relational/data-types)モデル。</span><span class="sxs-lookup"><span data-stu-id="eda18-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="eda18-169">地理の多角形のリング</span><span class="sxs-lookup"><span data-stu-id="eda18-169">Geography polygon rings</span></span>

<span data-ttu-id="eda18-170">使用する場合、`geography`列型、SQL Server が外部リング (またはシェル) に追加の要件を課すと内部リング (穴)。</span><span class="sxs-lookup"><span data-stu-id="eda18-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="eda18-171">外部リング指向左回りには、内部リングを時計回りにします。</span><span class="sxs-lookup"><span data-stu-id="eda18-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="eda18-172">NTS は、値をデータベースに送信する前に、これを検証します。</span><span class="sxs-lookup"><span data-stu-id="eda18-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="eda18-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="eda18-173">FullGlobe</span></span>

<span data-ttu-id="eda18-174">SQL Server が非標準の geometry 型を使用する場合は、全球を表す、`geography`列の型。</span><span class="sxs-lookup"><span data-stu-id="eda18-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="eda18-175">全球 (せずに、外部リング) に基づく多角形を表す方法もあります。</span><span class="sxs-lookup"><span data-stu-id="eda18-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="eda18-176">どちらもは NTS でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="eda18-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="eda18-177">FullGlobe とそれに基づく多角形は、NTS でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="eda18-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="eda18-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="eda18-178">SQLite</span></span>

<span data-ttu-id="eda18-179">SQLite を使用する場合のいくつか追加の情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="eda18-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="eda18-180">SpatiaLite をインストールします。</span><span class="sxs-lookup"><span data-stu-id="eda18-180">Installing SpatiaLite</span></span>

<span data-ttu-id="eda18-181">、Windows でネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="eda18-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="eda18-182">その他のプラットフォームでは、別途インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eda18-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="eda18-183">これは、通常のソフトウェアのパッケージ マネージャーを使用します。</span><span class="sxs-lookup"><span data-stu-id="eda18-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="eda18-184">たとえば、Ubuntu または macos の Homebrew での APT を使用できます。</span><span class="sxs-lookup"><span data-stu-id="eda18-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="eda18-185">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="eda18-185">Configuring SRID</span></span>

<span data-ttu-id="eda18-186">SpatiaLite、列を 1 列あたり SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eda18-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="eda18-187">既定の SRID は`0`します。</span><span class="sxs-lookup"><span data-stu-id="eda18-187">The default SRID is `0`.</span></span> <span data-ttu-id="eda18-188">ForSqliteHasSrid メソッドを使用して異なる SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="eda18-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="eda18-189">ディメンション</span><span class="sxs-lookup"><span data-stu-id="eda18-189">Dimension</span></span>

<span data-ttu-id="eda18-190">SRID と同様に、列のディメンション (または座標) がも指定されて、列の一部として。</span><span class="sxs-lookup"><span data-stu-id="eda18-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="eda18-191">既定の座標は X と適用を有効にする追加の座標 (z 座標および M) が ForSqliteHasDimension メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="eda18-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="eda18-192">翻訳済みの操作</span><span class="sxs-lookup"><span data-stu-id="eda18-192">Translated Operations</span></span>

<span data-ttu-id="eda18-193">次の表では、どの NTS メンバーは、EF Core プロバイダーによって SQL に変換を示します。</span><span class="sxs-lookup"><span data-stu-id="eda18-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="eda18-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="eda18-194">NetTopologySuite</span></span> | <span data-ttu-id="eda18-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-195">SQL Server (geometry)</span></span> | <span data-ttu-id="eda18-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="eda18-196">SQL Server (geography)</span></span> | <span data-ttu-id="eda18-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="eda18-197">SQLite</span></span> | <span data-ttu-id="eda18-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="eda18-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="eda18-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="eda18-199">Geometry.Area</span></span> | <span data-ttu-id="eda18-200">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-200">✔</span></span> | <span data-ttu-id="eda18-201">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-201">✔</span></span> | <span data-ttu-id="eda18-202">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-202">✔</span></span> | <span data-ttu-id="eda18-203">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-203">✔</span></span>
<span data-ttu-id="eda18-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="eda18-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="eda18-205">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-205">✔</span></span> | <span data-ttu-id="eda18-206">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-206">✔</span></span> | <span data-ttu-id="eda18-207">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-207">✔</span></span> | <span data-ttu-id="eda18-208">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-208">✔</span></span>
<span data-ttu-id="eda18-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="eda18-209">Geometry.AsText()</span></span> | <span data-ttu-id="eda18-210">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-210">✔</span></span> | <span data-ttu-id="eda18-211">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-211">✔</span></span> | <span data-ttu-id="eda18-212">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-212">✔</span></span> | <span data-ttu-id="eda18-213">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-213">✔</span></span>
<span data-ttu-id="eda18-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="eda18-214">Geometry.Boundary</span></span> | <span data-ttu-id="eda18-215">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-215">✔</span></span> | | <span data-ttu-id="eda18-216">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-216">✔</span></span> | <span data-ttu-id="eda18-217">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-217">✔</span></span>
<span data-ttu-id="eda18-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="eda18-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="eda18-219">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-219">✔</span></span> | <span data-ttu-id="eda18-220">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-220">✔</span></span> | <span data-ttu-id="eda18-221">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-221">✔</span></span> | <span data-ttu-id="eda18-222">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-222">✔</span></span>
<span data-ttu-id="eda18-223">Geometry.Buffer (int、double)</span><span class="sxs-lookup"><span data-stu-id="eda18-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="eda18-224">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-224">✔</span></span>
<span data-ttu-id="eda18-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="eda18-225">Geometry.Centroid</span></span> | <span data-ttu-id="eda18-226">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-226">✔</span></span> | | <span data-ttu-id="eda18-227">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-227">✔</span></span> | <span data-ttu-id="eda18-228">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-228">✔</span></span>
<span data-ttu-id="eda18-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="eda18-230">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-230">✔</span></span> | <span data-ttu-id="eda18-231">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-231">✔</span></span> | <span data-ttu-id="eda18-232">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-232">✔</span></span> | <span data-ttu-id="eda18-233">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-233">✔</span></span>
<span data-ttu-id="eda18-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="eda18-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="eda18-235">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-235">✔</span></span> | <span data-ttu-id="eda18-236">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-236">✔</span></span> | <span data-ttu-id="eda18-237">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-237">✔</span></span> | <span data-ttu-id="eda18-238">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-238">✔</span></span>
<span data-ttu-id="eda18-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="eda18-240">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-240">✔</span></span> | <span data-ttu-id="eda18-241">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-241">✔</span></span>
<span data-ttu-id="eda18-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="eda18-243">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-243">✔</span></span> | <span data-ttu-id="eda18-244">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-244">✔</span></span>
<span data-ttu-id="eda18-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="eda18-246">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-246">✔</span></span> | | <span data-ttu-id="eda18-247">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-247">✔</span></span> | <span data-ttu-id="eda18-248">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-248">✔</span></span>
<span data-ttu-id="eda18-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="eda18-250">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-250">✔</span></span> | <span data-ttu-id="eda18-251">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-251">✔</span></span> | <span data-ttu-id="eda18-252">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-252">✔</span></span> | <span data-ttu-id="eda18-253">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-253">✔</span></span>
<span data-ttu-id="eda18-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="eda18-254">Geometry.Dimension</span></span> | <span data-ttu-id="eda18-255">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-255">✔</span></span> | <span data-ttu-id="eda18-256">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-256">✔</span></span> | <span data-ttu-id="eda18-257">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-257">✔</span></span> | <span data-ttu-id="eda18-258">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-258">✔</span></span>
<span data-ttu-id="eda18-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="eda18-260">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-260">✔</span></span> | <span data-ttu-id="eda18-261">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-261">✔</span></span> | <span data-ttu-id="eda18-262">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-262">✔</span></span> | <span data-ttu-id="eda18-263">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-263">✔</span></span>
<span data-ttu-id="eda18-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="eda18-265">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-265">✔</span></span> | <span data-ttu-id="eda18-266">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-266">✔</span></span> | <span data-ttu-id="eda18-267">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-267">✔</span></span> | <span data-ttu-id="eda18-268">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-268">✔</span></span>
<span data-ttu-id="eda18-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="eda18-269">Geometry.Envelope</span></span> | <span data-ttu-id="eda18-270">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-270">✔</span></span> | | <span data-ttu-id="eda18-271">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-271">✔</span></span> | <span data-ttu-id="eda18-272">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-272">✔</span></span>
<span data-ttu-id="eda18-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="eda18-274">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-274">✔</span></span>
<span data-ttu-id="eda18-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="eda18-276">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-276">✔</span></span> | <span data-ttu-id="eda18-277">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-277">✔</span></span> | <span data-ttu-id="eda18-278">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-278">✔</span></span> | <span data-ttu-id="eda18-279">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-279">✔</span></span>
<span data-ttu-id="eda18-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="eda18-280">Geometry.GeometryType</span></span> | <span data-ttu-id="eda18-281">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-281">✔</span></span> | <span data-ttu-id="eda18-282">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-282">✔</span></span> | <span data-ttu-id="eda18-283">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-283">✔</span></span> | <span data-ttu-id="eda18-284">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-284">✔</span></span>
<span data-ttu-id="eda18-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="eda18-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="eda18-286">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-286">✔</span></span> | | <span data-ttu-id="eda18-287">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-287">✔</span></span> | <span data-ttu-id="eda18-288">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-288">✔</span></span>
<span data-ttu-id="eda18-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="eda18-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="eda18-290">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-290">✔</span></span> | | <span data-ttu-id="eda18-291">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-291">✔</span></span>
<span data-ttu-id="eda18-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="eda18-293">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-293">✔</span></span> | <span data-ttu-id="eda18-294">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-294">✔</span></span> | <span data-ttu-id="eda18-295">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-295">✔</span></span> | <span data-ttu-id="eda18-296">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-296">✔</span></span>
<span data-ttu-id="eda18-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="eda18-298">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-298">✔</span></span> | <span data-ttu-id="eda18-299">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-299">✔</span></span> | <span data-ttu-id="eda18-300">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-300">✔</span></span> | <span data-ttu-id="eda18-301">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-301">✔</span></span>
<span data-ttu-id="eda18-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="eda18-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="eda18-303">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-303">✔</span></span> | <span data-ttu-id="eda18-304">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-304">✔</span></span> | <span data-ttu-id="eda18-305">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-305">✔</span></span> | <span data-ttu-id="eda18-306">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-306">✔</span></span>
<span data-ttu-id="eda18-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="eda18-307">Geometry.IsSimple</span></span> | <span data-ttu-id="eda18-308">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-308">✔</span></span> | | <span data-ttu-id="eda18-309">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-309">✔</span></span> | <span data-ttu-id="eda18-310">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-310">✔</span></span>
<span data-ttu-id="eda18-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="eda18-311">Geometry.IsValid</span></span> | <span data-ttu-id="eda18-312">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-312">✔</span></span> | <span data-ttu-id="eda18-313">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-313">✔</span></span> | <span data-ttu-id="eda18-314">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-314">✔</span></span> | <span data-ttu-id="eda18-315">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-315">✔</span></span>
<span data-ttu-id="eda18-316">Geometry.IsWithinDistance (Geometry、double)</span><span class="sxs-lookup"><span data-stu-id="eda18-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="eda18-317">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-317">✔</span></span> | | <span data-ttu-id="eda18-318">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-318">✔</span></span>
<span data-ttu-id="eda18-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="eda18-319">Geometry.Length</span></span> | <span data-ttu-id="eda18-320">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-320">✔</span></span> | <span data-ttu-id="eda18-321">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-321">✔</span></span> | <span data-ttu-id="eda18-322">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-322">✔</span></span> | <span data-ttu-id="eda18-323">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-323">✔</span></span>
<span data-ttu-id="eda18-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="eda18-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="eda18-325">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-325">✔</span></span> | <span data-ttu-id="eda18-326">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-326">✔</span></span> | <span data-ttu-id="eda18-327">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-327">✔</span></span> | <span data-ttu-id="eda18-328">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-328">✔</span></span>
<span data-ttu-id="eda18-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="eda18-329">Geometry.NumPoints</span></span> | <span data-ttu-id="eda18-330">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-330">✔</span></span> | <span data-ttu-id="eda18-331">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-331">✔</span></span> | <span data-ttu-id="eda18-332">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-332">✔</span></span> | <span data-ttu-id="eda18-333">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-333">✔</span></span>
<span data-ttu-id="eda18-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="eda18-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="eda18-335">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-335">✔</span></span> | <span data-ttu-id="eda18-336">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-336">✔</span></span> | <span data-ttu-id="eda18-337">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-337">✔</span></span>
<span data-ttu-id="eda18-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="eda18-339">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-339">✔</span></span> | <span data-ttu-id="eda18-340">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-340">✔</span></span> | <span data-ttu-id="eda18-341">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-341">✔</span></span> | <span data-ttu-id="eda18-342">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-342">✔</span></span>
<span data-ttu-id="eda18-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="eda18-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="eda18-344">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-344">✔</span></span> | | <span data-ttu-id="eda18-345">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-345">✔</span></span> | <span data-ttu-id="eda18-346">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-346">✔</span></span>
<span data-ttu-id="eda18-347">Geometry.Relate (Geometry、文字列)</span><span class="sxs-lookup"><span data-stu-id="eda18-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="eda18-348">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-348">✔</span></span> | | <span data-ttu-id="eda18-349">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-349">✔</span></span> | <span data-ttu-id="eda18-350">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-350">✔</span></span>
<span data-ttu-id="eda18-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="eda18-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="eda18-352">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-352">✔</span></span> | <span data-ttu-id="eda18-353">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-353">✔</span></span>
<span data-ttu-id="eda18-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="eda18-354">Geometry.SRID</span></span> | <span data-ttu-id="eda18-355">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-355">✔</span></span> | <span data-ttu-id="eda18-356">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-356">✔</span></span> | <span data-ttu-id="eda18-357">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-357">✔</span></span> | <span data-ttu-id="eda18-358">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-358">✔</span></span>
<span data-ttu-id="eda18-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="eda18-360">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-360">✔</span></span> | <span data-ttu-id="eda18-361">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-361">✔</span></span> | <span data-ttu-id="eda18-362">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-362">✔</span></span> | <span data-ttu-id="eda18-363">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-363">✔</span></span>
<span data-ttu-id="eda18-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="eda18-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="eda18-365">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-365">✔</span></span> | <span data-ttu-id="eda18-366">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-366">✔</span></span> | <span data-ttu-id="eda18-367">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-367">✔</span></span> | <span data-ttu-id="eda18-368">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-368">✔</span></span>
<span data-ttu-id="eda18-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="eda18-369">Geometry.ToText()</span></span> | <span data-ttu-id="eda18-370">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-370">✔</span></span> | <span data-ttu-id="eda18-371">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-371">✔</span></span> | <span data-ttu-id="eda18-372">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-372">✔</span></span> | <span data-ttu-id="eda18-373">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-373">✔</span></span>
<span data-ttu-id="eda18-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="eda18-375">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-375">✔</span></span> | | <span data-ttu-id="eda18-376">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-376">✔</span></span> | <span data-ttu-id="eda18-377">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-377">✔</span></span>
<span data-ttu-id="eda18-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="eda18-378">Geometry.Union()</span></span> | | | <span data-ttu-id="eda18-379">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-379">✔</span></span>
<span data-ttu-id="eda18-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="eda18-381">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-381">✔</span></span> | <span data-ttu-id="eda18-382">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-382">✔</span></span> | <span data-ttu-id="eda18-383">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-383">✔</span></span> | <span data-ttu-id="eda18-384">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-384">✔</span></span>
<span data-ttu-id="eda18-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="eda18-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="eda18-386">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-386">✔</span></span> | <span data-ttu-id="eda18-387">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-387">✔</span></span> | <span data-ttu-id="eda18-388">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-388">✔</span></span> | <span data-ttu-id="eda18-389">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-389">✔</span></span>
<span data-ttu-id="eda18-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="eda18-390">GeometryCollection.Count</span></span> | <span data-ttu-id="eda18-391">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-391">✔</span></span> | <span data-ttu-id="eda18-392">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-392">✔</span></span> | <span data-ttu-id="eda18-393">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-393">✔</span></span> | <span data-ttu-id="eda18-394">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-394">✔</span></span>
<span data-ttu-id="eda18-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="eda18-395">GeometryCollection[int]</span></span> | <span data-ttu-id="eda18-396">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-396">✔</span></span> | <span data-ttu-id="eda18-397">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-397">✔</span></span> | <span data-ttu-id="eda18-398">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-398">✔</span></span> | <span data-ttu-id="eda18-399">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-399">✔</span></span>
<span data-ttu-id="eda18-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="eda18-400">LineString.Count</span></span> | <span data-ttu-id="eda18-401">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-401">✔</span></span> | <span data-ttu-id="eda18-402">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-402">✔</span></span> | <span data-ttu-id="eda18-403">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-403">✔</span></span> | <span data-ttu-id="eda18-404">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-404">✔</span></span>
<span data-ttu-id="eda18-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="eda18-405">LineString.EndPoint</span></span> | <span data-ttu-id="eda18-406">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-406">✔</span></span> | <span data-ttu-id="eda18-407">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-407">✔</span></span> | <span data-ttu-id="eda18-408">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-408">✔</span></span> | <span data-ttu-id="eda18-409">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-409">✔</span></span>
<span data-ttu-id="eda18-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="eda18-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="eda18-411">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-411">✔</span></span> | <span data-ttu-id="eda18-412">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-412">✔</span></span> | <span data-ttu-id="eda18-413">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-413">✔</span></span> | <span data-ttu-id="eda18-414">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-414">✔</span></span>
<span data-ttu-id="eda18-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="eda18-415">LineString.IsClosed</span></span> | <span data-ttu-id="eda18-416">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-416">✔</span></span> | <span data-ttu-id="eda18-417">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-417">✔</span></span> | <span data-ttu-id="eda18-418">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-418">✔</span></span> | <span data-ttu-id="eda18-419">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-419">✔</span></span>
<span data-ttu-id="eda18-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="eda18-420">LineString.IsRing</span></span> | <span data-ttu-id="eda18-421">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-421">✔</span></span> | | <span data-ttu-id="eda18-422">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-422">✔</span></span> | <span data-ttu-id="eda18-423">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-423">✔</span></span>
<span data-ttu-id="eda18-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="eda18-424">LineString.StartPoint</span></span> | <span data-ttu-id="eda18-425">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-425">✔</span></span> | <span data-ttu-id="eda18-426">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-426">✔</span></span> | <span data-ttu-id="eda18-427">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-427">✔</span></span> | <span data-ttu-id="eda18-428">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-428">✔</span></span>
<span data-ttu-id="eda18-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="eda18-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="eda18-430">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-430">✔</span></span> | <span data-ttu-id="eda18-431">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-431">✔</span></span> | <span data-ttu-id="eda18-432">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-432">✔</span></span> | <span data-ttu-id="eda18-433">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-433">✔</span></span>
<span data-ttu-id="eda18-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="eda18-434">Point.M</span></span> | <span data-ttu-id="eda18-435">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-435">✔</span></span> | <span data-ttu-id="eda18-436">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-436">✔</span></span> | <span data-ttu-id="eda18-437">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-437">✔</span></span> | <span data-ttu-id="eda18-438">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-438">✔</span></span>
<span data-ttu-id="eda18-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="eda18-439">Point.X</span></span> | <span data-ttu-id="eda18-440">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-440">✔</span></span> | <span data-ttu-id="eda18-441">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-441">✔</span></span> | <span data-ttu-id="eda18-442">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-442">✔</span></span> | <span data-ttu-id="eda18-443">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-443">✔</span></span>
<span data-ttu-id="eda18-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="eda18-444">Point.Y</span></span> | <span data-ttu-id="eda18-445">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-445">✔</span></span> | <span data-ttu-id="eda18-446">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-446">✔</span></span> | <span data-ttu-id="eda18-447">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-447">✔</span></span> | <span data-ttu-id="eda18-448">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-448">✔</span></span>
<span data-ttu-id="eda18-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="eda18-449">Point.Z</span></span> | <span data-ttu-id="eda18-450">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-450">✔</span></span> | <span data-ttu-id="eda18-451">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-451">✔</span></span> | <span data-ttu-id="eda18-452">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-452">✔</span></span> | <span data-ttu-id="eda18-453">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-453">✔</span></span>
<span data-ttu-id="eda18-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="eda18-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="eda18-455">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-455">✔</span></span> | <span data-ttu-id="eda18-456">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-456">✔</span></span> | <span data-ttu-id="eda18-457">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-457">✔</span></span> | <span data-ttu-id="eda18-458">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-458">✔</span></span>
<span data-ttu-id="eda18-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="eda18-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="eda18-460">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-460">✔</span></span> | <span data-ttu-id="eda18-461">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-461">✔</span></span> | <span data-ttu-id="eda18-462">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-462">✔</span></span> | <span data-ttu-id="eda18-463">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-463">✔</span></span>
<span data-ttu-id="eda18-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="eda18-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="eda18-465">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-465">✔</span></span> | <span data-ttu-id="eda18-466">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-466">✔</span></span> | <span data-ttu-id="eda18-467">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-467">✔</span></span> | <span data-ttu-id="eda18-468">✔</span><span class="sxs-lookup"><span data-stu-id="eda18-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eda18-469">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="eda18-469">Additional resources</span></span>

* [<span data-ttu-id="eda18-470">SQL Server での空間データ</span><span class="sxs-lookup"><span data-stu-id="eda18-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="eda18-471">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="eda18-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="eda18-472">Npgsql 空間ドキュメント</span><span class="sxs-lookup"><span data-stu-id="eda18-472">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="eda18-473">PostGIS ドキュメント</span><span class="sxs-lookup"><span data-stu-id="eda18-473">PostGIS Documentation</span></span>](http://postgis.net/documentation/)

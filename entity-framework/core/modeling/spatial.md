---
title: 空間データ - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 49c18758af2f2383ea082ead2f6df4c022152b36
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688788"
---
# <a name="spatial-data"></a><span data-ttu-id="6ba4f-102">空間データ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="6ba4f-103">この機能は、EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="6ba4f-104">空間データは、物理的な場所とオブジェクトの形状を表します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="6ba4f-105">多くのデータベースは、インデックスを作成し、その他のデータと併せてクエリを実行できるように、この種類のデータのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="6ba4f-106">一般的なシナリオにはの場所から指定した距離内のオブジェクトの照会や境界には、特定の場所が含まれています。 オブジェクトの選択が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="6ba4f-107">EF Core を使用して空間データ型マッピングをサポートする、 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="6ba4f-108">インストール</span><span class="sxs-lookup"><span data-stu-id="6ba4f-108">Installing</span></span>

<span data-ttu-id="6ba4f-109">EF Core の空間データを使用するには、適切なサポートの NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="6ba4f-110">インストールする必要があると、パッケージは、使用しているプロバイダーに依存します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="6ba4f-111">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="6ba4f-111">EF Core Provider</span></span>                        | <span data-ttu-id="6ba4f-112">空間の NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="6ba4f-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="6ba4f-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="6ba4f-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="6ba4f-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="6ba4f-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="6ba4f-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="6ba4f-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="6ba4f-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="6ba4f-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="6ba4f-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="6ba4f-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="6ba4f-121">リバース エンジニア リング</span><span class="sxs-lookup"><span data-stu-id="6ba4f-121">Reverse engineering</span></span>

<span data-ttu-id="6ba4f-122">空間の NuGet パッケージのも有効にする[リバース エンジニア リング](../managing-schemas/scaffolding.md)が空間のプロパティを使用したモデルは、パッケージをインストールする必要があります***する前に***を実行している`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="6ba4f-123">ない場合は、列の型のマッピングを検出していないに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="6ba4f-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="6ba4f-125">NetTopologySuite は、.NET の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="6ba4f-126">EF Core では、モデルの NTS 型を使用して、空間データをデータベースで型のマッピングを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="6ba4f-127">NTS 経由での空間型へのマッピングを有効にするには、プロバイダーの DbContext オプション ビルダーを UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="6ba4f-128">たとえば、SQL Server で、次のように呼び出すことは。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="6ba4f-129">いくつかの空間データの種類があります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-129">There are several spatial data types.</span></span> <span data-ttu-id="6ba4f-130">使用する種類を許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="6ba4f-131">モデルのプロパティを使用できる NTS 型の階層を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="6ba4f-132">内にある、`NetTopologySuite.Geometries`名前空間。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="6ba4f-133">GeoAPI パッケージ内の対応するインターフェイス (`GeoAPI.Geometries`名前空間) こともできます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="6ba4f-134">geometry</span><span class="sxs-lookup"><span data-stu-id="6ba4f-134">Geometry</span></span>
  * <span data-ttu-id="6ba4f-135">ポイント</span><span class="sxs-lookup"><span data-stu-id="6ba4f-135">Point</span></span>
  * <span data-ttu-id="6ba4f-136">LineString</span><span class="sxs-lookup"><span data-stu-id="6ba4f-136">LineString</span></span>
  * <span data-ttu-id="6ba4f-137">多角形</span><span class="sxs-lookup"><span data-stu-id="6ba4f-137">Polygon</span></span>
  * <span data-ttu-id="6ba4f-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="6ba4f-138">GeometryCollection</span></span>
    * <span data-ttu-id="6ba4f-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="6ba4f-139">MultiPoint</span></span>
    * <span data-ttu-id="6ba4f-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="6ba4f-140">MultiLineString</span></span>
    * <span data-ttu-id="6ba4f-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="6ba4f-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="6ba4f-142">CircularString、CompoundCurve、および CurePolygon は NTS によってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="6ba4f-143">基本の Geometry 型を使用すると、どの種類のプロパティで指定する図形ができます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="6ba4f-144">次のエンティティ クラスを使用して、内のテーブルにマップする可能性があります、 [Wide World Importers サンプル データベース](http://go.microsoft.com/fwlink/?LinkID=800630)します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="6ba4f-145">値を作成します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-145">Creating values</span></span>

<span data-ttu-id="6ba4f-146">Geometry オブジェクトを作成するのにコンス トラクターを使用できます。ただし、NTS では、geometry ファクトリを代わりに使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="6ba4f-147">これは、既定の SRID (使用する、座標空間参照系) を指定することができ、できます (計算中に使用)、有効桁数のモデルと (どの座標--ディメンションを決定する座標のシーケンスなどのより高度な制御メジャーを利用)。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="6ba4f-148">4326 は、wgs-84、GPS およびその他の地理的なシステムで使用される標準を参照します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="6ba4f-149">緯度と経度</span><span class="sxs-lookup"><span data-stu-id="6ba4f-149">Longitude and Latitude</span></span>

<span data-ttu-id="6ba4f-150">NTS で座標がの観点では X と Y の値。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="6ba4f-151">経度と緯度を表現するには、緯度経度と Y の X を使用します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="6ba4f-152">これは**下位**から、`latitude, longitude`形式は通常、これらの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="6ba4f-153">クライアントの操作中に SRID が無視されます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="6ba4f-154">NTS は、操作中に、SRID 値を無視します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="6ba4f-155">平面座標系が想定しています。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="6ba4f-156">つまり、経度と緯度、距離、長さ、および領域がないメーターの角度でになりますようないくつかのクライアントで評価される値の観点からの座標を指定する場合。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="6ba4f-157">意味のある値は、まずにプロジェクトの座標などのライブラリを使用して、もう 1 つの座標系を[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)これらの値を計算する前にします。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="6ba4f-158">操作が SQL 経由での EF Core によってサーバーで評価される場合は、結果のユニットは、データベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="6ba4f-159">ProjNet4GeoAPI を使用して、2 つの都市間の距離を計算する例を示します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="6ba4f-160">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-160">Querying Data</span></span>

<span data-ttu-id="6ba4f-161">LINQ では、NTS メソッドとデータベース機能として使用できるプロパティは SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="6ba4f-162">たとえば、距離と Contains メソッドは、次のクエリに変換されます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="6ba4f-163">この記事の最後にある表では、さまざまな EF Core プロバイダーによってサポートされるメンバーを示します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="6ba4f-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="6ba4f-164">SQL Server</span></span>

<span data-ttu-id="6ba4f-165">SQL Server を使用している場合の注意すべき点があります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="6ba4f-166">地理またはジオメトリ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-166">Geography or geometry</span></span>

<span data-ttu-id="6ba4f-167">既定では、空間プロパティのマップ`geography`SQL Server 内の列。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="6ba4f-168">使用する`geometry`、[列の型を構成する](xref:core/modeling/relational/data-types)モデル。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="6ba4f-169">地理の多角形のリング</span><span class="sxs-lookup"><span data-stu-id="6ba4f-169">Geography polygon rings</span></span>

<span data-ttu-id="6ba4f-170">使用する場合、`geography`列型、SQL Server が外部リング (またはシェル) に追加の要件を課すと内部リング (穴)。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="6ba4f-171">外部リング指向左回りには、内部リングを時計回りにします。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="6ba4f-172">NTS は、値をデータベースに送信する前に、これを検証します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="6ba4f-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="6ba4f-173">FullGlobe</span></span>

<span data-ttu-id="6ba4f-174">SQL Server が非標準の geometry 型を使用する場合は、全球を表す、`geography`列の型。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="6ba4f-175">全球 (せずに、外部リング) に基づく多角形を表す方法もあります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="6ba4f-176">どちらもは NTS でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="6ba4f-177">FullGlobe とそれに基づく多角形は、NTS でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="6ba4f-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-178">SQLite</span></span>

<span data-ttu-id="6ba4f-179">SQLite を使用する場合のいくつか追加の情報を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="6ba4f-180">SpatiaLite をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-180">Installing SpatiaLite</span></span>

<span data-ttu-id="6ba4f-181">、Windows でネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="6ba4f-182">その他のプラットフォームでは、別途インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="6ba4f-183">これは、通常のソフトウェアのパッケージ マネージャーを使用します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="6ba4f-184">たとえば、Ubuntu または macos の Homebrew での APT を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="6ba4f-185">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="6ba4f-185">Configuring SRID</span></span>

<span data-ttu-id="6ba4f-186">SpatiaLite、列を 1 列あたり SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="6ba4f-187">既定の SRID は`0`します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-187">The default SRID is `0`.</span></span> <span data-ttu-id="6ba4f-188">ForSqliteHasSrid メソッドを使用して異なる SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="6ba4f-189">ディメンション</span><span class="sxs-lookup"><span data-stu-id="6ba4f-189">Dimension</span></span>

<span data-ttu-id="6ba4f-190">SRID と同様に、列のディメンション (または座標) がも指定されて、列の一部として。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="6ba4f-191">既定の座標は X と適用を有効にする追加の座標 (z 座標および M) が ForSqliteHasDimension メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="6ba4f-192">翻訳済みの操作</span><span class="sxs-lookup"><span data-stu-id="6ba4f-192">Translated Operations</span></span>

<span data-ttu-id="6ba4f-193">次の表では、どの NTS メンバーは、EF Core プロバイダーによって SQL に変換を示します。</span><span class="sxs-lookup"><span data-stu-id="6ba4f-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="6ba4f-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-194">NetTopologySuite</span></span> | <span data-ttu-id="6ba4f-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-195">SQL Server (geometry)</span></span> | <span data-ttu-id="6ba4f-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-196">SQL Server (geography)</span></span> | <span data-ttu-id="6ba4f-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="6ba4f-197">SQLite</span></span> | <span data-ttu-id="6ba4f-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="6ba4f-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="6ba4f-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="6ba4f-199">Geometry.Area</span></span> | <span data-ttu-id="6ba4f-200">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-200">✔</span></span> | <span data-ttu-id="6ba4f-201">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-201">✔</span></span> | <span data-ttu-id="6ba4f-202">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-202">✔</span></span> | <span data-ttu-id="6ba4f-203">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-203">✔</span></span>
<span data-ttu-id="6ba4f-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="6ba4f-205">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-205">✔</span></span> | <span data-ttu-id="6ba4f-206">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-206">✔</span></span> | <span data-ttu-id="6ba4f-207">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-207">✔</span></span> | <span data-ttu-id="6ba4f-208">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-208">✔</span></span>
<span data-ttu-id="6ba4f-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-209">Geometry.AsText()</span></span> | <span data-ttu-id="6ba4f-210">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-210">✔</span></span> | <span data-ttu-id="6ba4f-211">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-211">✔</span></span> | <span data-ttu-id="6ba4f-212">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-212">✔</span></span> | <span data-ttu-id="6ba4f-213">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-213">✔</span></span>
<span data-ttu-id="6ba4f-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="6ba4f-214">Geometry.Boundary</span></span> | <span data-ttu-id="6ba4f-215">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-215">✔</span></span> | | <span data-ttu-id="6ba4f-216">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-216">✔</span></span> | <span data-ttu-id="6ba4f-217">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-217">✔</span></span>
<span data-ttu-id="6ba4f-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="6ba4f-219">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-219">✔</span></span> | <span data-ttu-id="6ba4f-220">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-220">✔</span></span> | <span data-ttu-id="6ba4f-221">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-221">✔</span></span> | <span data-ttu-id="6ba4f-222">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-222">✔</span></span>
<span data-ttu-id="6ba4f-223">Geometry.Buffer (int、double)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="6ba4f-224">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-224">✔</span></span>
<span data-ttu-id="6ba4f-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="6ba4f-225">Geometry.Centroid</span></span> | <span data-ttu-id="6ba4f-226">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-226">✔</span></span> | | <span data-ttu-id="6ba4f-227">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-227">✔</span></span> | <span data-ttu-id="6ba4f-228">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-228">✔</span></span>
<span data-ttu-id="6ba4f-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="6ba4f-230">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-230">✔</span></span> | <span data-ttu-id="6ba4f-231">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-231">✔</span></span> | <span data-ttu-id="6ba4f-232">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-232">✔</span></span> | <span data-ttu-id="6ba4f-233">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-233">✔</span></span>
<span data-ttu-id="6ba4f-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="6ba4f-235">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-235">✔</span></span> | <span data-ttu-id="6ba4f-236">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-236">✔</span></span> | <span data-ttu-id="6ba4f-237">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-237">✔</span></span> | <span data-ttu-id="6ba4f-238">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-238">✔</span></span>
<span data-ttu-id="6ba4f-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="6ba4f-240">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-240">✔</span></span> | <span data-ttu-id="6ba4f-241">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-241">✔</span></span>
<span data-ttu-id="6ba4f-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="6ba4f-243">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-243">✔</span></span> | <span data-ttu-id="6ba4f-244">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-244">✔</span></span>
<span data-ttu-id="6ba4f-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="6ba4f-246">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-246">✔</span></span> | | <span data-ttu-id="6ba4f-247">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-247">✔</span></span> | <span data-ttu-id="6ba4f-248">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-248">✔</span></span>
<span data-ttu-id="6ba4f-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="6ba4f-250">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-250">✔</span></span> | <span data-ttu-id="6ba4f-251">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-251">✔</span></span> | <span data-ttu-id="6ba4f-252">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-252">✔</span></span> | <span data-ttu-id="6ba4f-253">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-253">✔</span></span>
<span data-ttu-id="6ba4f-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="6ba4f-254">Geometry.Dimension</span></span> | <span data-ttu-id="6ba4f-255">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-255">✔</span></span> | <span data-ttu-id="6ba4f-256">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-256">✔</span></span> | <span data-ttu-id="6ba4f-257">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-257">✔</span></span> | <span data-ttu-id="6ba4f-258">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-258">✔</span></span>
<span data-ttu-id="6ba4f-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="6ba4f-260">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-260">✔</span></span> | <span data-ttu-id="6ba4f-261">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-261">✔</span></span> | <span data-ttu-id="6ba4f-262">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-262">✔</span></span> | <span data-ttu-id="6ba4f-263">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-263">✔</span></span>
<span data-ttu-id="6ba4f-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="6ba4f-265">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-265">✔</span></span> | <span data-ttu-id="6ba4f-266">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-266">✔</span></span> | <span data-ttu-id="6ba4f-267">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-267">✔</span></span> | <span data-ttu-id="6ba4f-268">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-268">✔</span></span>
<span data-ttu-id="6ba4f-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="6ba4f-269">Geometry.Envelope</span></span> | <span data-ttu-id="6ba4f-270">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-270">✔</span></span> | | <span data-ttu-id="6ba4f-271">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-271">✔</span></span> | <span data-ttu-id="6ba4f-272">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-272">✔</span></span>
<span data-ttu-id="6ba4f-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="6ba4f-274">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-274">✔</span></span>
<span data-ttu-id="6ba4f-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="6ba4f-276">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-276">✔</span></span> | <span data-ttu-id="6ba4f-277">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-277">✔</span></span> | <span data-ttu-id="6ba4f-278">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-278">✔</span></span> | <span data-ttu-id="6ba4f-279">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-279">✔</span></span>
<span data-ttu-id="6ba4f-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="6ba4f-280">Geometry.GeometryType</span></span> | <span data-ttu-id="6ba4f-281">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-281">✔</span></span> | <span data-ttu-id="6ba4f-282">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-282">✔</span></span> | <span data-ttu-id="6ba4f-283">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-283">✔</span></span> | <span data-ttu-id="6ba4f-284">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-284">✔</span></span>
<span data-ttu-id="6ba4f-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="6ba4f-286">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-286">✔</span></span> | | <span data-ttu-id="6ba4f-287">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-287">✔</span></span> | <span data-ttu-id="6ba4f-288">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-288">✔</span></span>
<span data-ttu-id="6ba4f-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="6ba4f-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="6ba4f-290">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-290">✔</span></span> | | <span data-ttu-id="6ba4f-291">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-291">✔</span></span>
<span data-ttu-id="6ba4f-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="6ba4f-293">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-293">✔</span></span> | <span data-ttu-id="6ba4f-294">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-294">✔</span></span> | <span data-ttu-id="6ba4f-295">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-295">✔</span></span> | <span data-ttu-id="6ba4f-296">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-296">✔</span></span>
<span data-ttu-id="6ba4f-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="6ba4f-298">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-298">✔</span></span> | <span data-ttu-id="6ba4f-299">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-299">✔</span></span> | <span data-ttu-id="6ba4f-300">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-300">✔</span></span> | <span data-ttu-id="6ba4f-301">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-301">✔</span></span>
<span data-ttu-id="6ba4f-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="6ba4f-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="6ba4f-303">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-303">✔</span></span> | <span data-ttu-id="6ba4f-304">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-304">✔</span></span> | <span data-ttu-id="6ba4f-305">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-305">✔</span></span> | <span data-ttu-id="6ba4f-306">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-306">✔</span></span>
<span data-ttu-id="6ba4f-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="6ba4f-307">Geometry.IsSimple</span></span> | <span data-ttu-id="6ba4f-308">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-308">✔</span></span> | | <span data-ttu-id="6ba4f-309">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-309">✔</span></span> | <span data-ttu-id="6ba4f-310">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-310">✔</span></span>
<span data-ttu-id="6ba4f-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="6ba4f-311">Geometry.IsValid</span></span> | <span data-ttu-id="6ba4f-312">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-312">✔</span></span> | <span data-ttu-id="6ba4f-313">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-313">✔</span></span> | <span data-ttu-id="6ba4f-314">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-314">✔</span></span> | <span data-ttu-id="6ba4f-315">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-315">✔</span></span>
<span data-ttu-id="6ba4f-316">Geometry.IsWithinDistance (Geometry、double)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="6ba4f-317">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-317">✔</span></span> | | <span data-ttu-id="6ba4f-318">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-318">✔</span></span>
<span data-ttu-id="6ba4f-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="6ba4f-319">Geometry.Length</span></span> | <span data-ttu-id="6ba4f-320">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-320">✔</span></span> | <span data-ttu-id="6ba4f-321">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-321">✔</span></span> | <span data-ttu-id="6ba4f-322">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-322">✔</span></span> | <span data-ttu-id="6ba4f-323">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-323">✔</span></span>
<span data-ttu-id="6ba4f-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="6ba4f-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="6ba4f-325">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-325">✔</span></span> | <span data-ttu-id="6ba4f-326">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-326">✔</span></span> | <span data-ttu-id="6ba4f-327">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-327">✔</span></span> | <span data-ttu-id="6ba4f-328">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-328">✔</span></span>
<span data-ttu-id="6ba4f-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="6ba4f-329">Geometry.NumPoints</span></span> | <span data-ttu-id="6ba4f-330">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-330">✔</span></span> | <span data-ttu-id="6ba4f-331">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-331">✔</span></span> | <span data-ttu-id="6ba4f-332">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-332">✔</span></span> | <span data-ttu-id="6ba4f-333">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-333">✔</span></span>
<span data-ttu-id="6ba4f-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="6ba4f-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="6ba4f-335">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-335">✔</span></span> | <span data-ttu-id="6ba4f-336">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-336">✔</span></span> | <span data-ttu-id="6ba4f-337">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-337">✔</span></span>
<span data-ttu-id="6ba4f-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="6ba4f-339">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-339">✔</span></span> | <span data-ttu-id="6ba4f-340">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-340">✔</span></span> | <span data-ttu-id="6ba4f-341">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-341">✔</span></span> | <span data-ttu-id="6ba4f-342">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-342">✔</span></span>
<span data-ttu-id="6ba4f-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="6ba4f-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="6ba4f-344">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-344">✔</span></span> | | <span data-ttu-id="6ba4f-345">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-345">✔</span></span> | <span data-ttu-id="6ba4f-346">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-346">✔</span></span>
<span data-ttu-id="6ba4f-347">Geometry.Relate (Geometry、文字列)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="6ba4f-348">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-348">✔</span></span> | | <span data-ttu-id="6ba4f-349">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-349">✔</span></span> | <span data-ttu-id="6ba4f-350">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-350">✔</span></span>
<span data-ttu-id="6ba4f-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="6ba4f-352">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-352">✔</span></span> | <span data-ttu-id="6ba4f-353">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-353">✔</span></span>
<span data-ttu-id="6ba4f-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="6ba4f-354">Geometry.SRID</span></span> | <span data-ttu-id="6ba4f-355">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-355">✔</span></span> | <span data-ttu-id="6ba4f-356">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-356">✔</span></span> | <span data-ttu-id="6ba4f-357">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-357">✔</span></span> | <span data-ttu-id="6ba4f-358">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-358">✔</span></span>
<span data-ttu-id="6ba4f-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="6ba4f-360">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-360">✔</span></span> | <span data-ttu-id="6ba4f-361">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-361">✔</span></span> | <span data-ttu-id="6ba4f-362">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-362">✔</span></span> | <span data-ttu-id="6ba4f-363">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-363">✔</span></span>
<span data-ttu-id="6ba4f-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="6ba4f-365">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-365">✔</span></span> | <span data-ttu-id="6ba4f-366">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-366">✔</span></span> | <span data-ttu-id="6ba4f-367">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-367">✔</span></span> | <span data-ttu-id="6ba4f-368">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-368">✔</span></span>
<span data-ttu-id="6ba4f-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-369">Geometry.ToText()</span></span> | <span data-ttu-id="6ba4f-370">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-370">✔</span></span> | <span data-ttu-id="6ba4f-371">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-371">✔</span></span> | <span data-ttu-id="6ba4f-372">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-372">✔</span></span> | <span data-ttu-id="6ba4f-373">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-373">✔</span></span>
<span data-ttu-id="6ba4f-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="6ba4f-375">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-375">✔</span></span> | | <span data-ttu-id="6ba4f-376">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-376">✔</span></span> | <span data-ttu-id="6ba4f-377">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-377">✔</span></span>
<span data-ttu-id="6ba4f-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="6ba4f-378">Geometry.Union()</span></span> | | | <span data-ttu-id="6ba4f-379">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-379">✔</span></span>
<span data-ttu-id="6ba4f-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="6ba4f-381">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-381">✔</span></span> | <span data-ttu-id="6ba4f-382">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-382">✔</span></span> | <span data-ttu-id="6ba4f-383">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-383">✔</span></span> | <span data-ttu-id="6ba4f-384">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-384">✔</span></span>
<span data-ttu-id="6ba4f-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="6ba4f-386">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-386">✔</span></span> | <span data-ttu-id="6ba4f-387">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-387">✔</span></span> | <span data-ttu-id="6ba4f-388">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-388">✔</span></span> | <span data-ttu-id="6ba4f-389">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-389">✔</span></span>
<span data-ttu-id="6ba4f-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="6ba4f-390">GeometryCollection.Count</span></span> | <span data-ttu-id="6ba4f-391">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-391">✔</span></span> | <span data-ttu-id="6ba4f-392">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-392">✔</span></span> | <span data-ttu-id="6ba4f-393">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-393">✔</span></span> | <span data-ttu-id="6ba4f-394">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-394">✔</span></span>
<span data-ttu-id="6ba4f-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="6ba4f-395">GeometryCollection[int]</span></span> | <span data-ttu-id="6ba4f-396">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-396">✔</span></span> | <span data-ttu-id="6ba4f-397">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-397">✔</span></span> | <span data-ttu-id="6ba4f-398">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-398">✔</span></span> | <span data-ttu-id="6ba4f-399">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-399">✔</span></span>
<span data-ttu-id="6ba4f-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="6ba4f-400">LineString.Count</span></span> | <span data-ttu-id="6ba4f-401">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-401">✔</span></span> | <span data-ttu-id="6ba4f-402">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-402">✔</span></span> | <span data-ttu-id="6ba4f-403">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-403">✔</span></span> | <span data-ttu-id="6ba4f-404">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-404">✔</span></span>
<span data-ttu-id="6ba4f-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="6ba4f-405">LineString.EndPoint</span></span> | <span data-ttu-id="6ba4f-406">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-406">✔</span></span> | <span data-ttu-id="6ba4f-407">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-407">✔</span></span> | <span data-ttu-id="6ba4f-408">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-408">✔</span></span> | <span data-ttu-id="6ba4f-409">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-409">✔</span></span>
<span data-ttu-id="6ba4f-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="6ba4f-411">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-411">✔</span></span> | <span data-ttu-id="6ba4f-412">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-412">✔</span></span> | <span data-ttu-id="6ba4f-413">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-413">✔</span></span> | <span data-ttu-id="6ba4f-414">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-414">✔</span></span>
<span data-ttu-id="6ba4f-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="6ba4f-415">LineString.IsClosed</span></span> | <span data-ttu-id="6ba4f-416">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-416">✔</span></span> | <span data-ttu-id="6ba4f-417">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-417">✔</span></span> | <span data-ttu-id="6ba4f-418">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-418">✔</span></span> | <span data-ttu-id="6ba4f-419">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-419">✔</span></span>
<span data-ttu-id="6ba4f-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="6ba4f-420">LineString.IsRing</span></span> | <span data-ttu-id="6ba4f-421">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-421">✔</span></span> | | <span data-ttu-id="6ba4f-422">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-422">✔</span></span> | <span data-ttu-id="6ba4f-423">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-423">✔</span></span>
<span data-ttu-id="6ba4f-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="6ba4f-424">LineString.StartPoint</span></span> | <span data-ttu-id="6ba4f-425">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-425">✔</span></span> | <span data-ttu-id="6ba4f-426">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-426">✔</span></span> | <span data-ttu-id="6ba4f-427">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-427">✔</span></span> | <span data-ttu-id="6ba4f-428">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-428">✔</span></span>
<span data-ttu-id="6ba4f-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="6ba4f-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="6ba4f-430">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-430">✔</span></span> | <span data-ttu-id="6ba4f-431">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-431">✔</span></span> | <span data-ttu-id="6ba4f-432">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-432">✔</span></span> | <span data-ttu-id="6ba4f-433">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-433">✔</span></span>
<span data-ttu-id="6ba4f-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="6ba4f-434">Point.M</span></span> | <span data-ttu-id="6ba4f-435">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-435">✔</span></span> | <span data-ttu-id="6ba4f-436">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-436">✔</span></span> | <span data-ttu-id="6ba4f-437">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-437">✔</span></span> | <span data-ttu-id="6ba4f-438">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-438">✔</span></span>
<span data-ttu-id="6ba4f-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="6ba4f-439">Point.X</span></span> | <span data-ttu-id="6ba4f-440">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-440">✔</span></span> | <span data-ttu-id="6ba4f-441">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-441">✔</span></span> | <span data-ttu-id="6ba4f-442">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-442">✔</span></span> | <span data-ttu-id="6ba4f-443">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-443">✔</span></span>
<span data-ttu-id="6ba4f-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="6ba4f-444">Point.Y</span></span> | <span data-ttu-id="6ba4f-445">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-445">✔</span></span> | <span data-ttu-id="6ba4f-446">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-446">✔</span></span> | <span data-ttu-id="6ba4f-447">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-447">✔</span></span> | <span data-ttu-id="6ba4f-448">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-448">✔</span></span>
<span data-ttu-id="6ba4f-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="6ba4f-449">Point.Z</span></span> | <span data-ttu-id="6ba4f-450">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-450">✔</span></span> | <span data-ttu-id="6ba4f-451">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-451">✔</span></span> | <span data-ttu-id="6ba4f-452">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-452">✔</span></span> | <span data-ttu-id="6ba4f-453">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-453">✔</span></span>
<span data-ttu-id="6ba4f-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="6ba4f-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="6ba4f-455">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-455">✔</span></span> | <span data-ttu-id="6ba4f-456">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-456">✔</span></span> | <span data-ttu-id="6ba4f-457">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-457">✔</span></span> | <span data-ttu-id="6ba4f-458">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-458">✔</span></span>
<span data-ttu-id="6ba4f-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="6ba4f-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="6ba4f-460">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-460">✔</span></span> | <span data-ttu-id="6ba4f-461">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-461">✔</span></span> | <span data-ttu-id="6ba4f-462">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-462">✔</span></span> | <span data-ttu-id="6ba4f-463">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-463">✔</span></span>
<span data-ttu-id="6ba4f-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="6ba4f-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="6ba4f-465">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-465">✔</span></span> | <span data-ttu-id="6ba4f-466">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-466">✔</span></span> | <span data-ttu-id="6ba4f-467">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-467">✔</span></span> | <span data-ttu-id="6ba4f-468">✔</span><span class="sxs-lookup"><span data-stu-id="6ba4f-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ba4f-469">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6ba4f-469">Additional resources</span></span>

* [<span data-ttu-id="6ba4f-470">SQL Server での空間データ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="6ba4f-471">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="6ba4f-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="6ba4f-472">PostGIS ドキュメント</span><span class="sxs-lookup"><span data-stu-id="6ba4f-472">PostGIS Documentation</span></span>](http://postgis.net/documentation/)

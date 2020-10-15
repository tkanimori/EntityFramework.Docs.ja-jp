---
title: 空間データ-EF Core
description: Entity Framework Core モデルでの空間データの使用
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 2c0cd7a8acf7e4b58eadf8805afa1fe4a1d6e949
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063934"
---
# <a name="spatial-data"></a><span data-ttu-id="2aa5d-103">空間データ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="2aa5d-104">この機能は EF Core 2.2 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="2aa5d-105">空間データは、オブジェクトの物理的な位置と形状を表します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="2aa5d-106">多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="2aa5d-107">一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="2aa5d-108">EF Core では、NetTopologySuite 空間ライブラリを使用した空間データ型へのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="2aa5d-109">インストール</span><span class="sxs-lookup"><span data-stu-id="2aa5d-109">Installing</span></span>

<span data-ttu-id="2aa5d-110">EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="2aa5d-111">どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="2aa5d-112">EF Core プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2aa5d-112">EF Core Provider</span></span>                        | <span data-ttu-id="2aa5d-113">空間 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="2aa5d-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="2aa5d-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="2aa5d-115">NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)</span><span class="sxs-lookup"><span data-stu-id="2aa5d-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="2aa5d-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="2aa5d-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="2aa5d-117">NetTopologySuite (Microsoft EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="2aa5d-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="2aa5d-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="2aa5d-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="2aa5d-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="2aa5d-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="2aa5d-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="2aa5d-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="2aa5d-121">Npgsql. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2aa5d-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="2aa5d-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="2aa5d-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="2aa5d-123">Pomelo. MySql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="2aa5d-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="2aa5d-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="2aa5d-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="2aa5d-125">NetTopologySuite (devart. データ...</span><span class="sxs-lookup"><span data-stu-id="2aa5d-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="2aa5d-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="2aa5d-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="2aa5d-127">NetTopologySuite (devart. データ...)</span><span class="sxs-lookup"><span data-stu-id="2aa5d-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="2aa5d-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="2aa5d-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="2aa5d-129">NetTopologySuite (devart. データ. SQLite)</span><span class="sxs-lookup"><span data-stu-id="2aa5d-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="2aa5d-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2aa5d-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="2aa5d-131">Teradata. EntityFrameworkCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="2aa5d-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="2aa5d-132">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="2aa5d-132">NetTopologySuite</span></span>

<span data-ttu-id="2aa5d-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) は、.net 用の空間ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="2aa5d-134">EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="2aa5d-135">NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="2aa5d-136">たとえば、SQL Server では、次のようにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="2aa5d-137">空間データ型はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-137">There are several spatial data types.</span></span> <span data-ttu-id="2aa5d-138">使用する種類は、許可する図形の種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="2aa5d-139">ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="2aa5d-140">これらは名前空間内にあり `NetTopologySuite.Geometries` ます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="2aa5d-141">ジオメトリ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-141">Geometry</span></span>
  * <span data-ttu-id="2aa5d-142">ポイント</span><span class="sxs-lookup"><span data-stu-id="2aa5d-142">Point</span></span>
  * <span data-ttu-id="2aa5d-143">LineString</span><span class="sxs-lookup"><span data-stu-id="2aa5d-143">LineString</span></span>
  * <span data-ttu-id="2aa5d-144">多角形</span><span class="sxs-lookup"><span data-stu-id="2aa5d-144">Polygon</span></span>
  * <span data-ttu-id="2aa5d-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="2aa5d-145">GeometryCollection</span></span>
    * <span data-ttu-id="2aa5d-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="2aa5d-146">MultiPoint</span></span>
    * <span data-ttu-id="2aa5d-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="2aa5d-147">MultiLineString</span></span>
    * <span data-ttu-id="2aa5d-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="2aa5d-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="2aa5d-149">CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="2aa5d-150">基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="2aa5d-151">経度と緯度</span><span class="sxs-lookup"><span data-stu-id="2aa5d-151">Longitude and Latitude</span></span>

<span data-ttu-id="2aa5d-152">NTS 内の座標は、X 値と Y 値で表現されます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="2aa5d-153">経度と緯度を表すには、経度には X、緯度には Y を使用します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="2aa5d-154">これは、通常、これらの値が表示されるのとは **逆** の形式であることに注意して `latitude, longitude` ください。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="2aa5d-155">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-155">Querying Data</span></span>

<span data-ttu-id="2aa5d-156">次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="2aa5d-157">LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="2aa5d-158">たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="2aa5d-159">サポートされているメソッドについては、プロバイダーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="2aa5d-160">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="2aa5d-160">Reverse engineering</span></span>

<span data-ttu-id="2aa5d-161">空間 NuGet パッケージでは、空間プロパティを使用してモデルの [リバースエンジニアリング](xref:core/managing-schemas/scaffolding) を行うこともできますが、またはを実行する ***前*** にパッケージをインストールする必要があり `Scaffold-DbContext` `dotnet ef dbcontext scaffold` ます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="2aa5d-162">そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="2aa5d-163">クライアント操作中に SRID が無視されました</span><span class="sxs-lookup"><span data-stu-id="2aa5d-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="2aa5d-164">NTS は、操作中に SRID の値を無視します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="2aa5d-165">平面座標系を前提としています。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="2aa5d-166">これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="2aa5d-167">意味のある値については、まず、 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) のようなライブラリを使用して、これらの値を計算する前に、別の座標系の座標を射影する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="2aa5d-168">操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-168">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="2aa5d-169">ProjNet4GeoAPI を使用して2つの都市間の距離を計算する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-169">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="2aa5d-170">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-170">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2aa5d-171">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2aa5d-171">Additional resources</span></span>

<span data-ttu-id="2aa5d-172">空間データの操作の詳細については、プロバイダーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2aa5d-172">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

* [<span data-ttu-id="2aa5d-173">SQL Server プロバイダーの空間データ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-173">Spatial Data in the SQL Server Provider</span></span>](xref:core/providers/sql-server/spatial)
* [<span data-ttu-id="2aa5d-174">SQLite プロバイダーの空間データ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-174">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="2aa5d-175">Npgsql プロバイダーの空間データ</span><span class="sxs-lookup"><span data-stu-id="2aa5d-175">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="2aa5d-176">NetTopologySuite のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2aa5d-176">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)

---
title: SQLite データベースプロバイダー-空間データ-EF Core
description: Entity Framework Core SQLite データベースプロバイダーでの空間データの使用
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066525"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="f47c1-103">SQLite EF Core プロバイダーの空間データ</span><span class="sxs-lookup"><span data-stu-id="f47c1-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="f47c1-104">このページには、SQLite データベースプロバイダーでの空間データの使用に関する追加情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f47c1-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="f47c1-105">EF Core での空間データの使用に関する一般的な情報については、主な [空間データ](xref:core/modeling/spatial) のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f47c1-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="f47c1-106">SpatiaLite のインストール</span><span class="sxs-lookup"><span data-stu-id="f47c1-106">Installing SpatiaLite</span></span>

<span data-ttu-id="f47c1-107">Windows では、ネイティブ mod_spatialite ライブラリは NuGet パッケージの依存関係として配布されます。</span><span class="sxs-lookup"><span data-stu-id="f47c1-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="f47c1-108">他のプラットフォームでは、別のプラットフォームをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f47c1-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="f47c1-109">通常、これはソフトウェアパッケージマネージャーを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="f47c1-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="f47c1-110">たとえば、Debian および Ubuntu で APT を使用できます。MacOS での Homebrew です。</span><span class="sxs-lookup"><span data-stu-id="f47c1-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="f47c1-111">残念ながら、新しいバージョンの PROJ (SpatiaLite の依存関係) は EF の既定の [SQLitePCLRaw バンドル](/dotnet/standard/data/sqlite/custom-versions#bundles)と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="f47c1-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="f47c1-112">この問題を回避するには、代わりにシステム SQLite ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="f47c1-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="f47c1-113">**MacOS**では、Homebrew のバージョンの SQLite を使用するように、アプリを実行する前に環境変数を設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="f47c1-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="f47c1-114">Visual Studio for Mac では、これを [プロジェクト > プロジェクトオプション] の下に設定して **> 構成を実行 > > 既定値**</span><span class="sxs-lookup"><span data-stu-id="f47c1-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="f47c1-115">SRID の構成</span><span class="sxs-lookup"><span data-stu-id="f47c1-115">Configuring SRID</span></span>

<span data-ttu-id="f47c1-116">SpatiaLite では、列ごとに SRID を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f47c1-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="f47c1-117">既定の SRID は `0` です。</span><span class="sxs-lookup"><span data-stu-id="f47c1-117">The default SRID is `0`.</span></span> <span data-ttu-id="f47c1-118">ForSqliteHasSrid メソッドを使用して、別の SRID を指定します。</span><span class="sxs-lookup"><span data-stu-id="f47c1-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="f47c1-119">4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。</span><span class="sxs-lookup"><span data-stu-id="f47c1-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="f47c1-120">Dimension</span><span class="sxs-lookup"><span data-stu-id="f47c1-120">Dimension</span></span>

<span data-ttu-id="f47c1-121">列の既定のディメンション (座標) は、X と Y です。Z や M などの追加の座標を有効にするには、列の型を構成します。</span><span class="sxs-lookup"><span data-stu-id="f47c1-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="f47c1-122">空間関数のマッピング</span><span class="sxs-lookup"><span data-stu-id="f47c1-122">Spatial function mappings</span></span>

<span data-ttu-id="f47c1-123">次の表は、どの [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) メンバーがどの SQL 関数に変換されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="f47c1-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="f47c1-124">.NET</span><span class="sxs-lookup"><span data-stu-id="f47c1-124">.NET</span></span>                                        | <span data-ttu-id="f47c1-125">SQL</span><span class="sxs-lookup"><span data-stu-id="f47c1-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="f47c1-126">geometry.領域</span><span class="sxs-lookup"><span data-stu-id="f47c1-126">geometry.Area</span></span>                               | <span data-ttu-id="f47c1-127">区分 ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-127">Area(@geometry)</span></span>
<span data-ttu-id="f47c1-128">geometry.AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="f47c1-129">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="f47c1-130">geometry.AsText ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-130">geometry.AsText()</span></span>                           | <span data-ttu-id="f47c1-131">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-131">AsText(@geometry)</span></span>
<span data-ttu-id="f47c1-132">geometry.範囲</span><span class="sxs-lookup"><span data-stu-id="f47c1-132">geometry.Boundary</span></span>                           | <span data-ttu-id="f47c1-133">境界 ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-133">Boundary(@geometry)</span></span>
<span data-ttu-id="f47c1-134">geometry.バッファー (距離)</span><span class="sxs-lookup"><span data-stu-id="f47c1-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="f47c1-135">Buffer ( @geometry , @distance )</span><span class="sxs-lookup"><span data-stu-id="f47c1-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="f47c1-136">geometry.Buffer (distance, quadrantSegments)</span><span class="sxs-lookup"><span data-stu-id="f47c1-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="f47c1-137">Buffer ( @geometry , @distance , @quadrantSegments )</span><span class="sxs-lookup"><span data-stu-id="f47c1-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="f47c1-138">geometry.重心</span><span class="sxs-lookup"><span data-stu-id="f47c1-138">geometry.Centroid</span></span>                           | <span data-ttu-id="f47c1-139">重心 ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-139">Centroid(@geometry)</span></span>
<span data-ttu-id="f47c1-140">geometry.(G) を含む</span><span class="sxs-lookup"><span data-stu-id="f47c1-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="f47c1-141">Contains ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-142">geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="f47c1-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="f47c1-143">ConvexHull ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="f47c1-144">geometry.(G)</span><span class="sxs-lookup"><span data-stu-id="f47c1-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="f47c1-145">( @geometry 、 @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-146">geometry.カバー (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="f47c1-147">カバー ( @geometry 、 @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-148">geometry.交差 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="f47c1-149">( @geometry ,) を交差する @g</span><span class="sxs-lookup"><span data-stu-id="f47c1-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-150">geometry.差 (その他)</span><span class="sxs-lookup"><span data-stu-id="f47c1-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="f47c1-151">相違点 ( @geometry 、 @other )</span><span class="sxs-lookup"><span data-stu-id="f47c1-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="f47c1-152">geometry.多次元</span><span class="sxs-lookup"><span data-stu-id="f47c1-152">geometry.Dimension</span></span>                          | <span data-ttu-id="f47c1-153">Dimension ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-153">Dimension(@geometry)</span></span>
<span data-ttu-id="f47c1-154">geometry.不整合 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="f47c1-155">不整合 ( @geometry 、 @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-156">geometry.距離 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="f47c1-157">Distance ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-158">geometry.差出人</span><span class="sxs-lookup"><span data-stu-id="f47c1-158">geometry.Envelope</span></span>                           | <span data-ttu-id="f47c1-159">Envelope ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-159">Envelope(@geometry)</span></span>
<span data-ttu-id="f47c1-160">geometry.EqualsTopologically (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="f47c1-161">Equals ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-162">geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="f47c1-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="f47c1-163">GeometryType ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="f47c1-164">geometry.GetGeometryN (n)</span><span class="sxs-lookup"><span data-stu-id="f47c1-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="f47c1-165">GeometryN ( @geometry , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="f47c1-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="f47c1-166">geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="f47c1-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="f47c1-167">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="f47c1-168">geometry.共通部分 (その他)</span><span class="sxs-lookup"><span data-stu-id="f47c1-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="f47c1-169">積集合 ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="f47c1-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="f47c1-170">geometry.交差 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="f47c1-171">( @geometry ,) と交差します @g 。</span><span class="sxs-lookup"><span data-stu-id="f47c1-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-172">geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="f47c1-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="f47c1-173">IsEmpty ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="f47c1-174">geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="f47c1-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="f47c1-175">IsSimple ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="f47c1-176">geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="f47c1-176">geometry.IsValid</span></span>                            | <span data-ttu-id="f47c1-177">IsValid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-177">IsValid(@geometry)</span></span>
<span data-ttu-id="f47c1-178">geometry.Iswithin Distance (ジオメトリ、距離)</span><span class="sxs-lookup"><span data-stu-id="f47c1-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="f47c1-179">Distance ( @geometry , @geom ) <= @distance</span><span class="sxs-lookup"><span data-stu-id="f47c1-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="f47c1-180">geometry.数</span><span class="sxs-lookup"><span data-stu-id="f47c1-180">geometry.Length</span></span>                             | <span data-ttu-id="f47c1-181">GLength ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-181">GLength(@geometry)</span></span>
<span data-ttu-id="f47c1-182">geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="f47c1-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="f47c1-183">NumGeometries ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="f47c1-184">geometry.X.numpoints</span><span class="sxs-lookup"><span data-stu-id="f47c1-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="f47c1-185">NumPoints ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="f47c1-186">geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="f47c1-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="f47c1-187">CASE GeometryType ( @geometry ) の場合、' POINT '、1...終わり</span><span class="sxs-lookup"><span data-stu-id="f47c1-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="f47c1-188">geometry.重複 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="f47c1-189">重複 ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-190">geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="f47c1-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="f47c1-191">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="f47c1-192">geometry.関連付け (g, intersectionPattern)</span><span class="sxs-lookup"><span data-stu-id="f47c1-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="f47c1-193">関連付け ( @geometry , @g , @intersectionPattern )</span><span class="sxs-lookup"><span data-stu-id="f47c1-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="f47c1-194">geometry.Reverse ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="f47c1-195">ST_Reverse ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="f47c1-196">geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="f47c1-196">geometry.SRID</span></span>                               | <span data-ttu-id="f47c1-197">SRID ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-197">SRID(@geometry)</span></span>
<span data-ttu-id="f47c1-198">geometry.SymmetricDifference (その他)</span><span class="sxs-lookup"><span data-stu-id="f47c1-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="f47c1-199">SymDifference ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="f47c1-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="f47c1-200">geometry.ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="f47c1-201">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="f47c1-202">geometry.ToText ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-202">geometry.ToText()</span></span>                           | <span data-ttu-id="f47c1-203">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-203">AsText(@geometry)</span></span>
<span data-ttu-id="f47c1-204">geometry.接触 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="f47c1-205">( @geometry 、) をタッチします @g 。</span><span class="sxs-lookup"><span data-stu-id="f47c1-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-206">geometry.Union ()</span><span class="sxs-lookup"><span data-stu-id="f47c1-206">geometry.Union()</span></span>                            | <span data-ttu-id="f47c1-207">UnaryUnion ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="f47c1-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="f47c1-208">geometry.共用体 (その他)</span><span class="sxs-lookup"><span data-stu-id="f47c1-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="f47c1-209">GUnion ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="f47c1-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="f47c1-210">geometry.内側 (g)</span><span class="sxs-lookup"><span data-stu-id="f47c1-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="f47c1-211">内 ( @geometry 、 @g )</span><span class="sxs-lookup"><span data-stu-id="f47c1-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="f47c1-212">geometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="f47c1-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="f47c1-213">GeometryN ( @geometryCollection , @i + 1)</span><span class="sxs-lookup"><span data-stu-id="f47c1-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="f47c1-214">geometryCollection</span><span class="sxs-lookup"><span data-stu-id="f47c1-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="f47c1-215">NumGeometries ( @geometryCollection )</span><span class="sxs-lookup"><span data-stu-id="f47c1-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="f47c1-216">lineString</span><span class="sxs-lookup"><span data-stu-id="f47c1-216">lineString.Count</span></span>                            | <span data-ttu-id="f47c1-217">NumPoints ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="f47c1-218">lineString</span><span class="sxs-lookup"><span data-stu-id="f47c1-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="f47c1-219">エンドポイント ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="f47c1-220">lineString n (n)</span><span class="sxs-lookup"><span data-stu-id="f47c1-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="f47c1-221">PointN ( @lineString , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="f47c1-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="f47c1-222">lineString</span><span class="sxs-lookup"><span data-stu-id="f47c1-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="f47c1-223">IsClosed ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="f47c1-224">lineString</span><span class="sxs-lookup"><span data-stu-id="f47c1-224">lineString.IsRing</span></span>                           | <span data-ttu-id="f47c1-225">IsRing ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-225">IsRing(@lineString)</span></span>
<span data-ttu-id="f47c1-226">lineString StartPoint</span><span class="sxs-lookup"><span data-stu-id="f47c1-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="f47c1-227">StartPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="f47c1-228">multiLineString</span><span class="sxs-lookup"><span data-stu-id="f47c1-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="f47c1-229">IsClosed ( @multiLineString )</span><span class="sxs-lookup"><span data-stu-id="f47c1-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="f47c1-230">視点.M</span><span class="sxs-lookup"><span data-stu-id="f47c1-230">point.M</span></span>                                     | <span data-ttu-id="f47c1-231">M ( @point )</span><span class="sxs-lookup"><span data-stu-id="f47c1-231">M(@point)</span></span>
<span data-ttu-id="f47c1-232">視点.閉じる</span><span class="sxs-lookup"><span data-stu-id="f47c1-232">point.X</span></span>                                     | <span data-ttu-id="f47c1-233">X ( @point )</span><span class="sxs-lookup"><span data-stu-id="f47c1-233">X(@point)</span></span>
<span data-ttu-id="f47c1-234">視点.前年</span><span class="sxs-lookup"><span data-stu-id="f47c1-234">point.Y</span></span>                                     | <span data-ttu-id="f47c1-235">Y ( @point )</span><span class="sxs-lookup"><span data-stu-id="f47c1-235">Y(@point)</span></span>
<span data-ttu-id="f47c1-236">視点.方向</span><span class="sxs-lookup"><span data-stu-id="f47c1-236">point.Z</span></span>                                     | <span data-ttu-id="f47c1-237">Z ( @point )</span><span class="sxs-lookup"><span data-stu-id="f47c1-237">Z(@point)</span></span>
<span data-ttu-id="f47c1-238">ポリゴン.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="f47c1-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="f47c1-239">ExteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="f47c1-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="f47c1-240">ポリゴン.GetInteriorRingN (n)</span><span class="sxs-lookup"><span data-stu-id="f47c1-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="f47c1-241">InteriorRingN ( @polygon , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="f47c1-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="f47c1-242">ポリゴン.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="f47c1-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="f47c1-243">NumInteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="f47c1-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f47c1-244">その他の資料</span><span class="sxs-lookup"><span data-stu-id="f47c1-244">Additional resources</span></span>

* [<span data-ttu-id="f47c1-245">SpatiaLite ホームページ</span><span class="sxs-lookup"><span data-stu-id="f47c1-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="f47c1-246">NetTopologySuite のドキュメント</span><span class="sxs-lookup"><span data-stu-id="f47c1-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)

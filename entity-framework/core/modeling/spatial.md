---
title: 空間データ-EF Core
description: Entity Framework Core モデルでの空間データの使用
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 721aa2628d17b89b79160f8f658f8ef0dd78d6a6
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543290"
---
# <a name="spatial-data"></a>空間データ

> [!NOTE]
> この機能は EF Core 2.2 で導入されました。

空間データは、オブジェクトの物理的な位置と形状を表します。 多くのデータベースでは、この種類のデータをサポートしているため、他のデータと共にインデックスを作成してクエリを実行できます。 一般的なシナリオとしては、ある場所からの特定の距離にあるオブジェクトのクエリ、または特定の場所を含む罫線を持つオブジェクトの選択などがあります。 EF Core では、NetTopologySuite 空間ライブラリを使用した空間データ型へのマッピングがサポートされています。

## <a name="installing"></a>インストール中

EF Core で空間データを使用するには、適切なサポート NuGet パッケージをインストールする必要があります。 どのパッケージをインストールする必要があるかは、使用しているプロバイダーによって異なります。

EF Core プロバイダー                        | 空間 NuGet パッケージ
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [NetTopologySuite (Microsoft EntityFrameworkCore. SqlServer.)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [NetTopologySuite (Microsoft EntityFrameworkCore)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [NetTopologySuite (devart. データ...](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [NetTopologySuite (devart. データ...)](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [NetTopologySuite (devart. データ. SQLite)](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. EntityFrameworkCore. NetTopologySuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>NetTopologySuite

[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) は、.net 用の空間ライブラリです。 EF Core を使用すると、モデルの NTS 型を使用して、データベース内の空間データ型にマッピングできます。

NTS 経由で空間型へのマッピングを有効にするには、プロバイダーの DbContext オプションビルダーで UseNetTopologySuite メソッドを呼び出します。 たとえば、SQL Server では、次のようにを呼び出します。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

空間データ型はいくつかあります。 使用する種類は、許可する図形の種類によって異なります。 ここでは、モデルのプロパティに使用できる NTS 型の階層を示します。 これらは名前空間内にあり `NetTopologySuite.Geometries` ます。

* ジオメトリ
  * ポイント
  * LineString
  * 多角形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString、CompoundCurve、および CurePolygon は、NTS ではサポートされていません。

基本 Geometry 型を使用すると、プロパティによって任意の型の図形を指定できます。

## <a name="longitude-and-latitude"></a>経度と緯度

NTS 内の座標は、X 値と Y 値で表現されます。 経度と緯度を表すには、経度には X、緯度には Y を使用します。 これは、通常、これらの値が表示されるのとは **逆** の形式であることに注意して `latitude, longitude` ください。

## <a name="querying-data"></a>データのクエリ

次のエンティティクラスは、 [Wide World のインポーターサンプルデータベース](https://go.microsoft.com/fwlink/?LinkID=800630)のテーブルにマップするために使用できます。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

LINQ では、データベース関数として使用できる NTS のメソッドとプロパティは、SQL に変換されます。 たとえば、Distance メソッドと Contains メソッドは、次のクエリでは変換されます。 サポートされているメソッドについては、プロバイダーのドキュメントを参照してください。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>リバースエンジニアリング

空間 NuGet パッケージでは、空間プロパティを使用してモデルの [リバースエンジニアリング](xref:core/managing-schemas/scaffolding) を行うこともできますが、またはを実行する ***前*** にパッケージをインストールする必要があり `Scaffold-DbContext` `dotnet ef dbcontext scaffold` ます。 そうしないと、列の型マッピングが見つからないことに関する警告が表示され、列はスキップされます。

## <a name="srid-ignored-during-client-operations"></a>クライアント操作中に SRID が無視されました

NTS は、操作中に SRID の値を無視します。 平面座標系を前提としています。 これは、経度と緯度の観点で座標を指定した場合、クライアントによって評価される値 (距離、長さ、領域など) は、メーターではなく、度数で表されることを意味します。 意味のある値については、まず、 [Projnet (GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI)などのライブラリを使用して、別の座標系の座標を射影する必要があります。

> [!NOTE]
> ProjNet4GeoAPI という古いパッケージでは **なく**、新しい [projnet NuGet パッケージ](https://www.nuget.org/packages/ProjNet/)を使用します。

操作が SQL を介して EF Core によってサーバーによって評価される場合、結果の単位はデータベースによって決定されます。

次に、ProjNet を使用して2つの都市間の距離を計算する例を示します。

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326は、GPS などの地理的システムで使用される標準の WGS 84 を指します。

## <a name="additional-resources"></a>その他のリソース

### <a name="database-specific-information"></a>データベース固有の情報

空間データの操作の詳細については、プロバイダーのドキュメントを参照してください。

* [SQL Server プロバイダーの空間データ](xref:core/providers/sql-server/spatial)
* [SQLite プロバイダーの空間データ](xref:core/providers/sqlite/spatial)
* [Npgsql プロバイダーの空間データ](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a>その他のリソース

* [NetTopologySuite のドキュメント](https://nettopologysuite.github.io/NetTopologySuite/)
* [EF Core コミュニティのスタンドアップセッション](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15)。空間データと NetTopologySuite に焦点を絞っています。

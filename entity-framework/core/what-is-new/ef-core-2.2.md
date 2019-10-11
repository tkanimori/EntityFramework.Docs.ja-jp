---
title: EF Core 2.2 の新機能 - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 5fcf7c6dfb4d8cb7928ef974af6deb52df7c63eb
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181371"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="526b0-102">EF Core 2.2 の新機能</span><span class="sxs-lookup"><span data-stu-id="526b0-102">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="526b0-103">空間データのサポート</span><span class="sxs-lookup"><span data-stu-id="526b0-103">Spatial data support</span></span>

<span data-ttu-id="526b0-104">オブジェクトの物理的な場所と形状を表現するために、空間データを使用できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-104">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="526b0-105">多くのデータベースでは、空間データの格納、インデックス付け、照会をネイティブで実行できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-105">Many databases can natively store, index, and query spatial data.</span></span> <span data-ttu-id="526b0-106">一般的なシナリオとしては、特定の距離内にあるオブジェクトにクエリを実行する場合や、特定の場所が多角形に含まれるかどうかテストする場合などがあります。</span><span class="sxs-lookup"><span data-stu-id="526b0-106">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="526b0-107">EF Core 2.2 では、[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) ライブラリからの型を使用して、さまざまなデータベースの空間データを操作できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="526b0-107">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="526b0-108">空間データのサポートは、一連のプロバイダー固有の拡張機能パッケージとして実装されています。</span><span class="sxs-lookup"><span data-stu-id="526b0-108">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="526b0-109">これらの各パッケージには、NTS 型用のマッピングとメソッド、および対応するデータベースの空間型と関数が用意されています。</span><span class="sxs-lookup"><span data-stu-id="526b0-109">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="526b0-110">現在、このようなプロバイダーの拡張機能は [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/)、([Npgsql プロジェクト](https://www.npgsql.org/)の) [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) で使用できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-110">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="526b0-111">空間型は、追加の拡張機能なしで、[EF Core のメモリ内プロバイダー](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/)と共に直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-111">Spatial types can be used directly with the [EF Core in-memory provider](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/) without additional extensions.</span></span>

<span data-ttu-id="526b0-112">プロバイダーの拡張機能をインストールすると、サポートされている型のプロパティをエンティティに追加できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-112">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="526b0-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="526b0-113">For example:</span></span>

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
``` 

<span data-ttu-id="526b0-114">その後、空間データを含むエンティティを保持できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-114">You can then persist entities with spatial data:</span></span>

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```
<span data-ttu-id="526b0-115">さらに、空間のデータと操作に基づくデータベース クエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="526b0-115">And you can execute database queries based on spatial data and operations:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="526b0-116">この機能について詳しくは、[空間型に関するドキュメント](xref:core/modeling/spatial)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="526b0-116">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span> 

## <a name="collections-of-owned-entities"></a><span data-ttu-id="526b0-117">所有エンティティのコレクション</span><span class="sxs-lookup"><span data-stu-id="526b0-117">Collections of owned entities</span></span>

<span data-ttu-id="526b0-118">EF Core 2.0 では、一対一の関連付けで所有権をモデル化する機能が追加されました。</span><span class="sxs-lookup"><span data-stu-id="526b0-118">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="526b0-119">EF Core 2.2 では、所有権を表す機能が一対多の関連付けに拡張されました。</span><span class="sxs-lookup"><span data-stu-id="526b0-119">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="526b0-120">所有権は、エンティティの使用方法を制限するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="526b0-120">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="526b0-121">たとえば、所有エンティティは:</span><span class="sxs-lookup"><span data-stu-id="526b0-121">For example, owned entities:</span></span>
- <span data-ttu-id="526b0-122">その他のエンティティ型のナビゲーション プロパティにのみ現れます。</span><span class="sxs-lookup"><span data-stu-id="526b0-122">Can only ever appear on navigation properties of other entity types.</span></span> 
- <span data-ttu-id="526b0-123">自動的に読み込まれ、DbContext とその所有者によってのみ追跡されます。</span><span class="sxs-lookup"><span data-stu-id="526b0-123">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="526b0-124">リレーショナル データベースでは、所有されているコレクションは通常の一対多の関連付けと同じように、所有者から個別のテーブルにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="526b0-124">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="526b0-125">ただし、ドキュメント指向のデータベースでは、所有者と同じドキュメント内で、(所有しているコレクションまたは参照で) 所有エンティティを入れ子にする予定です。</span><span class="sxs-lookup"><span data-stu-id="526b0-125">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="526b0-126">この機能を使用するには、新しい OwnsMany() API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="526b0-126">You can use the feature by calling the new OwnsMany() API:</span></span>

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="526b0-127">詳細については、[所有エンティティの更新に関するドキュメント](xref:core/modeling/owned-entities#collections-of-owned-types)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="526b0-127">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="526b0-128">クエリのタグ</span><span class="sxs-lookup"><span data-stu-id="526b0-128">Query tags</span></span>

<span data-ttu-id="526b0-129">この機能により、コード内の LINQ クエリと、ログでキャプチャされる生成済みの SQL クエリの関連付けが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="526b0-129">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="526b0-130">クエリのタグを利用するには、新しい TagWith() メソッドを使用して LINQ クエリに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="526b0-130">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="526b0-131">前の例の空間クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="526b0-131">Using the spatial query from a previous example:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="526b0-132">この LINQ クエリは次の SQL 出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="526b0-132">This LINQ query will produce the following SQL output:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="526b0-133">詳細については、[クエリのタグに関するドキュメント](xref:core/querying/tags)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="526b0-133">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span> 
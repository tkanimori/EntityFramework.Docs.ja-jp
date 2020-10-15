---
title: EF Core 2.2 の新機能 - EF Core
description: Entity Framework Core 2.2 での変更点と改善点
author: ajcvickers
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: ca71c7479254b25fe932e6abf43fe0fd8f1781b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065694"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="a4365-103">EF Core 2.2 の新機能</span><span class="sxs-lookup"><span data-stu-id="a4365-103">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="a4365-104">空間データのサポート</span><span class="sxs-lookup"><span data-stu-id="a4365-104">Spatial data support</span></span>

<span data-ttu-id="a4365-105">オブジェクトの物理的な場所と形状を表現するために、空間データを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-105">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="a4365-106">多くのデータベースでは、空間データの格納、インデックス付け、照会をネイティブで実行できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-106">Many databases can natively store, index, and query spatial data.</span></span>
<span data-ttu-id="a4365-107">一般的なシナリオとしては、特定の距離内にあるオブジェクトにクエリを実行する場合や、特定の場所が多角形に含まれるかどうかテストする場合などがあります。</span><span class="sxs-lookup"><span data-stu-id="a4365-107">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="a4365-108">EF Core 2.2 では、[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) ライブラリからの型を使用して、さまざまなデータベースの空間データを操作できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a4365-108">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="a4365-109">空間データのサポートは、一連のプロバイダー固有の拡張機能パッケージとして実装されています。</span><span class="sxs-lookup"><span data-stu-id="a4365-109">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="a4365-110">これらの各パッケージには、NTS 型用のマッピングとメソッド、および対応するデータベースの空間型と関数が用意されています。</span><span class="sxs-lookup"><span data-stu-id="a4365-110">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="a4365-111">現在、このようなプロバイダーの拡張機能は [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/)、([Npgsql プロジェクト](https://www.npgsql.org/)の) [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) で使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-111">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="a4365-112">空間型は、追加の拡張機能なしで、[EF Core のメモリ内プロバイダー](xref:core/providers/in-memory/index)と共に直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-112">Spatial types can be used directly with the [EF Core in-memory provider](xref:core/providers/in-memory/index) without additional extensions.</span></span>

<span data-ttu-id="a4365-113">プロバイダーの拡張機能をインストールすると、サポートされている型のプロパティをエンティティに追加できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-113">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="a4365-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a4365-114">For example:</span></span>

```csharp
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

<span data-ttu-id="a4365-115">その後、空間データを含むエンティティを保持できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-115">You can then persist entities with spatial data:</span></span>

```csharp
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

<span data-ttu-id="a4365-116">さらに、空間のデータと操作に基づくデータベース クエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="a4365-116">And you can execute database queries based on spatial data and operations:</span></span>

```csharp
var nearestFriends =
    (from f in context.Friends
    orderby f.Location.Distance(myLocation) descending
    select f).Take(5).ToList();
```

<span data-ttu-id="a4365-117">この機能について詳しくは、[空間型に関するドキュメント](xref:core/modeling/spatial)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a4365-117">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span>

## <a name="collections-of-owned-entities"></a><span data-ttu-id="a4365-118">所有エンティティのコレクション</span><span class="sxs-lookup"><span data-stu-id="a4365-118">Collections of owned entities</span></span>

<span data-ttu-id="a4365-119">EF Core 2.0 では、一対一の関連付けで所有権をモデル化する機能が追加されました。</span><span class="sxs-lookup"><span data-stu-id="a4365-119">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="a4365-120">EF Core 2.2 では、所有権を表す機能が一対多の関連付けに拡張されました。</span><span class="sxs-lookup"><span data-stu-id="a4365-120">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="a4365-121">所有権は、エンティティの使用方法を制限するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a4365-121">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="a4365-122">たとえば、所有エンティティは:</span><span class="sxs-lookup"><span data-stu-id="a4365-122">For example, owned entities:</span></span>

- <span data-ttu-id="a4365-123">その他のエンティティ型のナビゲーション プロパティにのみ現れます。</span><span class="sxs-lookup"><span data-stu-id="a4365-123">Can only ever appear on navigation properties of other entity types.</span></span>
- <span data-ttu-id="a4365-124">自動的に読み込まれ、DbContext とその所有者によってのみ追跡されます。</span><span class="sxs-lookup"><span data-stu-id="a4365-124">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="a4365-125">リレーショナル データベースでは、所有されているコレクションは通常の一対多の関連付けと同じように、所有者から個別のテーブルにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="a4365-125">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="a4365-126">ただし、ドキュメント指向のデータベースでは、所有者と同じドキュメント内で、(所有しているコレクションまたは参照で) 所有エンティティを入れ子にする予定です。</span><span class="sxs-lookup"><span data-stu-id="a4365-126">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="a4365-127">この機能を使用するには、新しい OwnsMany() API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4365-127">You can use the feature by calling the new OwnsMany() API:</span></span>

```csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="a4365-128">詳細については、[所有エンティティの更新に関するドキュメント](xref:core/modeling/owned-entities#collections-of-owned-types)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a4365-128">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="a4365-129">クエリのタグ</span><span class="sxs-lookup"><span data-stu-id="a4365-129">Query tags</span></span>

<span data-ttu-id="a4365-130">この機能により、コード内の LINQ クエリと、ログでキャプチャされる生成済みの SQL クエリの関連付けが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="a4365-130">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="a4365-131">クエリのタグを利用するには、新しい TagWith() メソッドを使用して LINQ クエリに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="a4365-131">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="a4365-132">前の例の空間クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a4365-132">Using the spatial query from a previous example:</span></span>

```csharp
var nearestFriends =
    (from f in context.Friends.TagWith(@"This is my spatial query!")
    orderby f.Location.Distance(myLocation) descending
    select f).Take(5).ToList();
```

<span data-ttu-id="a4365-133">この LINQ クエリは次の SQL 出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="a4365-133">This LINQ query will produce the following SQL output:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="a4365-134">詳細については、[クエリのタグに関するドキュメント](xref:core/querying/tags)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a4365-134">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span>

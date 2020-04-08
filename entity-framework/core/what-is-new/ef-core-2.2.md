---
title: EF Core 2.2 の新機能 - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: fb9de799753bebd7b4092cd8f4af74703dee3e45
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413553"
---
# <a name="new-features-in-ef-core-22"></a>EF Core 2.2 の新機能

## <a name="spatial-data-support"></a>空間データのサポート

オブジェクトの物理的な場所と形状を表現するために、空間データを使用できます。
多くのデータベースでは、空間データの格納、インデックス付け、照会をネイティブで実行できます。
一般的なシナリオとしては、特定の距離内にあるオブジェクトにクエリを実行する場合や、特定の場所が多角形に含まれるかどうかテストする場合などがあります。
EF Core 2.2 では、[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) ライブラリからの型を使用して、さまざまなデータベースの空間データを操作できるようになりました。

空間データのサポートは、一連のプロバイダー固有の拡張機能パッケージとして実装されています。
これらの各パッケージには、NTS 型用のマッピングとメソッド、および対応するデータベースの空間型と関数が用意されています。
現在、このようなプロバイダーの拡張機能は [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/)、([Npgsql プロジェクト](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/)の) [PostgreSQL](https://www.npgsql.org/) で使用できます。
空間型は、追加の拡張機能なしで、[EF Core のメモリ内プロバイダー](xref:core/providers/in-memory/index)と共に直接使用できます。

プロバイダーの拡張機能をインストールすると、サポートされている型のプロパティをエンティティに追加できます。 次に例を示します。

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

その後、空間データを含むエンティティを保持できます。

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

さらに、空間のデータと操作に基づくデータベース クエリを実行できます。

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

この機能について詳しくは、[空間型に関するドキュメント](xref:core/modeling/spatial)をご覧ください。

## <a name="collections-of-owned-entities"></a>所有エンティティのコレクション

EF Core 2.0 では、一対一の関連付けで所有権をモデル化する機能が追加されました。
EF Core 2.2 では、所有権を表す機能が一対多の関連付けに拡張されました。
所有権は、エンティティの使用方法を制限するのに役立ちます。

たとえば、所有エンティティは:

- その他のエンティティ型のナビゲーション プロパティにのみ現れます。
- 自動的に読み込まれ、DbContext とその所有者によってのみ追跡されます。

リレーショナル データベースでは、所有されているコレクションは通常の一対多の関連付けと同じように、所有者から個別のテーブルにマッピングされます。
ただし、ドキュメント指向のデータベースでは、所有者と同じドキュメント内で、(所有しているコレクションまたは参照で) 所有エンティティを入れ子にする予定です。

この機能を使用するには、新しい OwnsMany() API を呼び出します。

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

詳細については、[所有エンティティの更新に関するドキュメント](xref:core/modeling/owned-entities#collections-of-owned-types)をご覧ください。

## <a name="query-tags"></a>クエリのタグ

この機能により、コード内の LINQ クエリと、ログでキャプチャされる生成済みの SQL クエリの関連付けが簡略化されます。

クエリのタグを利用するには、新しい TagWith() メソッドを使用して LINQ クエリに注釈を付けます。
前の例の空間クエリを使用します。

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

この LINQ クエリは次の SQL 出力を生成します。

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

詳細については、[クエリのタグに関するドキュメント](xref:core/querying/tags)をご覧ください。

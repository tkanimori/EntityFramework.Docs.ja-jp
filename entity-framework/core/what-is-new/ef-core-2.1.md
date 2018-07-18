---
title: EF Core 2.1 の新機能 - EF Core
author: divega
ms.author: divega
ms.date: 2/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 44cbbc965755a694772dc4336ca2c1efc51fd6cd
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949232"
---
# <a name="new-features-in-ef-core-21"></a>EF Core 2.1 の新機能

多数のバグ修正と小さな機能およびパフォーマンスの強化だけでなく、EF Core 2.1 にはいくつかの魅力的な新機能が含まれています。

## <a name="lazy-loading"></a>遅延読み込み
EF Core に、すべてのユーザーが自身のナビゲーション プロパティを必要なときに読み込むことができるエンティティ クラスを作成するために必要なビルド ブロックが含まれるようになりました。 また、これらのビルド ブロックを利用して、最小限の変更を行ったエンティティ クラス (仮想ナビゲーション プロパティが指定されたクラスなど) に基づいて遅延読み込みプロキシ クラスを生成する、新しいパッケージ Microsoft.EntityFrameworkCore.Proxies も作成しました。

このトピックの詳細については、[遅延読み込みのセクション](xref:core/querying/related-data#lazy-loading)をご覧ください。

## <a name="parameters-in-entity-constructors"></a>エンティティ コンストラクターのパラメーター
遅延読み込みに必要なビルド ブロックの 1 つとして、コンストラクターでパラメーターを受け取るエンティティを作成できるようにしました。 パラメーターを使用して、プロパティ値、遅延読み込みデリゲート、およびサービスを挿入することができます。

このトピックの詳細については、[パラメーターが指定されたエンティティ コンストラクターに関するセクション](xref:core/modeling/constructors)をご覧ください。

## <a name="value-conversions"></a>値変換
これまで、EF Core では、基になるデータベース プロバイダーでネイティブにサポートされる型のプロパティしかマップできませんでした。 値は、変換されずに列とプロパティの間で相互にコピーされていました。 EF Core 2.1 以降では、列から取得された値を、値変換を適用して変換してから、プロパティに適用することができます。その逆も可能です。 必要に応じて、規則によって適用できる多くの変換に加え、列とプロパティ間のカスタム変換を登録できるようにする明示的な構成 API もあります。 この機能の用途をいくつか次に示します。

- 列挙型を文字列として格納する
- 符号なし整数と SQL Server とのマッピング
- プロパティ値の自動での暗号化と復号化

このトピックの詳細については、[値変換のセクション](xref:core/modeling/value-conversions)をご覧ください。  

## <a name="linq-groupby-translation"></a>LINQ GroupBy 変換
EF Core 2.1 より前のバージョンでは、GroupBy LINQ 演算子は常にメモリ内で評価されます。 これを最も一般的なケースの SQL GROUP BY 句に変換することがサポートされるようになりました。

この例では、さまざまな集計関数の計算に使用される GroupBy を使用したクエリを示しています。

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => Amount)
        });
```

対応する SQL 変換は、次のようになります。

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>データのシード処理
新しいリリースでは、データベースに入力する初期データを提供することができます。 EF6 とは異なり、データのシード処理がモデルの構成の一部としてエンティティ型に関連付けられています。 EF Core の移行では、データベースをモデルの新しいバージョンにアップグレードするときに、挿入、更新、または削除のどの操作を適用する必要があるかを自動的に計算できます。

次の例に示すように、これを使用して `OnModelCreating` の Post にシード データを構成できます。

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

このトピックの詳細については、[データのシード処理に関するセクション](xref:core/modeling/data-seeding)をご覧ください。  

## <a name="query-types"></a>クエリ型
EF Core モデルにクエリ型を含めることができるようになりました。 エンティティ型とは異なり、クエリ型には定義されたキーがないため、クエリ型を挿入、削除または更新することができません (つまり、読み取り専用です)。ただし、クエリによって直接返すことができます。 クエリ型の使用シナリオをいくつか次に示します。

- 主キーを使用しないビューへのマッピング
- 主キーを使用しないテーブルへのマッピング
- モデルで定義されているクエリへのマッピング
- `FromSql()` クエリの戻り値の型として機能

このトピックの詳細については、[クエリ型に関するセクション](xref:core/modeling/query-types)をご覧ください。

## <a name="include-for-derived-types"></a>派生型の Include
`Include` メソッドの式を記述する際に、派生型でのみ定義されているナビゲーション プロパティを指定できるようになりました。 `Include` の厳密に型指定されたバージョンについては、明示的なキャストまたは `as` 演算子を使用してサポートします。 また、`Include` の文字列バージョンの派生型で定義されているナビゲーション プロパティの名前の参照もサポートされるようになりました。

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

このトピックの詳細については、[派生型を使用した Include に関するセクション](xref:core/querying/related-data#include-on-derived-types)をご覧ください。

## <a name="systemtransactions-support"></a>System.Transactions のサポート
TransactionScope などの System.Transactions 機能が使用できるようになりました。 これは、この機能をサポートするデータベース プロバイダーを使用する場合、.NET Framework と .NET Core の両方で機能します。

このトピックの詳細については、[System.Transactions に関するセクション](xref:core/saving/transactions#using-systemtransactions)をご覧ください。

## <a name="better-column-ordering-in-initial-migration"></a>最初の移行での列の順序付けの改善
お客様のフィードバックに基づき、移行で最初に生成されるテーブルの列を、クラスで宣言されているプロパティと同じ順序になるように更新しました。 EF Core では、最初にテーブルが作成されると、その後で新しいメンバーを追加するときに順序を変更できないことに注意してください。

## <a name="optimization-of-correlated-subqueries"></a>相関サブクエリの最適化
プロジェクションでナビゲーション プロパティを使用して、ルート クエリからのデータと相関サブクエリからのデータが結合される多くの一般的なシナリオで、"N + 1" SQL クエリの実行を回避するようにクエリ変換を改善しました。 最適化には、サブクエリからの結果のバッファリングが必要なため、新しい動作を受け入れるようにクエリを変更する必要があります。

例として、次のクエリは、通常、Customers (顧客) の 1 つのクエリに加え、Orders の N 個 ("N" は返された顧客の数) の個別のクエリに変換されます。

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

適切な場所に `ToList()` を含めることで、最適化を有効にするバッファリングが Orders に適していることを示します。

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

このクエリは、Customers と Orders の 2 つの SQL クエリにのみ変換されることに注意してください。

## <a name="owned-attribute"></a>[Owned] 属性

`[Owned]` で型に注釈を付けるだけで、[所有エンティティ型](xref:core/modeling/owned-entities)を構成して、所有者エンティティをモデルに確実に追加できるようになりました。

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a>.NET Core SDK に含まれるコマンド ライン ツール dotnet-ef

_dotnet-ef_ コマンドは .NET Core SDK の一部になりました。したがって、移行を使用したり、既存のデータベースから DbContext をスキャフォールディングしたりするために、プロジェクトで DotNetCliToolReference を利用する必要がなくなります。

さまざまなバージョンの .NET Core SDK と EF Core でコマンド ライン ツールを有効にする方法の詳細については、「[ツールのインストール](xref:core/miscellaneous/cli/dotnet#installing-the-tools)」のセクションを参照してください。

## <a name="microsoftentityframeworkcoreabstractions-package"></a>Microsoft.EntityFrameworkCore.Abstractions パッケージ
この新しいパッケージに含まれる属性とインターフェイスをプロジェクトで使用すると、EF コアの依存関係全体を取得しなくても、EF Core の機能を使用できます。 たとえば、[Owned] 属性と ILazyLoader インターフェイスはここにあります。

## <a name="state-change-events"></a>状態変更イベント

`ChangeTracker` の新しい `Tracked` および `StateChanged` イベントを使用すると、エンティティの DbContext への移行またはその状態の変化に対応するロジックを記述することができます。

## <a name="raw-sql-parameter-analyzer"></a>生 SQL パラメーター アナライザー

EF Core に組み込まれた新しいコード アナライザーは、`FromSql` や `ExecuteSqlCommand` などの生 SQL API の安全ではない可能性がある使用法を検出します。 たとえば次のクエリでは、_minAge_ がパラメーター化されていないため、警告が発生します。

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a>データベース プロバイダーの互換性

EF Core 2.1 を操作する場合は、更新済みであるか、少なくともテスト済みであるプロバイダーで EF Core 2.1 を使用することをお勧めします。

> [!TIP]
> 予期しない非互換性や新機能で何らかの問題が見つかった場合、またはそれらに対するフィードバックがある場合は、[問題の追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues/new)を使用して報告してください。

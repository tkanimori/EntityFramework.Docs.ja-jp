---
title: EF Core 3.0 の新機能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: 528733d6eec33de2c9538541a6ed5be704b9d433
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005553"
---
# <a name="new-features-included-in-ef-core-30"></a>EF Core 3.0 に含まれる新機能

以下のリストには、EF Core 3.0 について計画されている主な新機能が含まれています。

EF Core 3.0 はメジャー リリースであり、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化である、[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)も多く含まれています。  

## <a name="linq-improvements"></a>LINQ の機能強化 

LINQ を使うと、好みの言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを提供できます。
しかし、LINQ では、任意の式 (メソッド呼び出しまたは操作) を含む複雑なクエリを無制限に記述できます。
これらのすべての組み合わせを処理することは、LINQ プロバイダーにとって以前よりかなり困難でした。
EF Core 3.0 では、より多くの式での SQL への変換、より多くのケースでの効率的なクエリの生成を可能にし、非効率的なクエリが検出されないことがないようにし、既存のアプリケーションやデータ プロバイダーを中断させることなく、新しいクエリの機能とパフォーマンスの強化を徐々に導入しやすくなるように、LINQ の実装を書き直しました。

### <a name="client-evaluation"></a>クライアントの評価

EF Core 3.0 の主な設計変更では、SQL またはパラメーターに変換できない LINQ 式の処理方法に関連があります。

最初のいくつかのバージョンの EF Core では、SQL に変換できるクエリの部分とクエリの残りの部分をクライアントに対して実行できる部分を特定するだけです。
状況によっては、このクライアント側の実行の種類が望ましいものとなる場合がありますが、その他の多くの場合では、非効率なクエリが発生する可能性があります。
たとえば、EF Core 2.2 では `Where()` 呼び出しで述語を変換できなかった場合、フィルターを使用せずに SQL ステートメントを実行し、データベースの行をすべて読み取り、メモリ内でフィルター処理します。
これは、データベースに少数の行が含まれている場合に許容される可能性がありますが、データベースの行が多い場合は、パフォーマンスに大きな問題が発生したり、アプリケーションのエラーが発生したりする可能性があります。
EF Core 3.0 では、最上位レベルのプロジェクション (`Select()` への最後の呼び出し) でのみ発生するように、クライアント評価を制限しています。
EF Core 3.0 でクエリ内の他の場所では変換できない式を検出した場合、ランタイムの例外がスローされます。

## <a name="cosmos-db-support"></a>Cosmos DB のサポート 

EF Core 用の Cosmos DB プロバイダーでは、EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにします。
その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。
プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。

## <a name="c-80-support"></a>C# 8.0 のサポート

EF Core 3.0 では、C# 8.0 の新機能の一部を利用します。

### <a name="asynchronous-streams"></a>非同期ストリーム

非同期クエリの結果は、新しい標準 `IAsyncEnumerable<T>` インターフェイスを使用して公開されるようになり、`await foreach` を使用して使用できるようになりました。

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Proccess(o);
} 
```

### <a name="nullable-reference-types"></a>null 許容参照型 

コード内でこの新しい機能が有効になっている場合、EF Core はデータベース内の列およびリレーションシップの NULL 値の許容を決定するために、参照型 (文字列プロパティやナビゲーション プロパティなどのプリミティブ型) のプロパティの NULL 値の許容を判断できます。

## <a name="interception"></a>interception

EF Core 3.0 の新しいインターセプト API を使用すると、接続を開いたり、トランザクションを開始したり、コマンドを実行したりするなど、EF Core の通常の動作の一部として実行される低レベルのデータベース操作の結果をプログラムによって監視および変更できます。 

## <a name="reverse-engineering-of-database-views"></a>データベース ビューのリバース エンジニアリング

キーなしのエンティティ型 (以前の[クエリ型](xref:core/modeling/query-types)) は、データベースから読み取ることができるデータを表しますが、更新はできません。
この特性によって、ほとんどのシナリオのマッピング データベース ビューに最適なものとなるため、データベース ビューのリバース エンジニアリング時にキーなしのエンティティ型の作成を自動化しました。

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>プリンシパルとテーブルを共有する依存エンティティが省略可能になりました

EF Core 3.0 以降では、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` なしで `Order` を追加することができるようになり、主キー以外のすべての `OrderDetails` プロパティは NULL 値が許可される列にマップされます。

クエリ時には、必須プロパティのいずれかに値がない場合、または主キー以外に必須プロパティがなく、すべてのプロパティが `null` である場合、EF Core によって `OrderDetails` が `null` に設定されます。

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>.NET Core での EF 6.3

多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると多大な労力が必要となる場合があります。
そのため、.NET Core 3.0 上で最新バージョンの EF 6 を実行できるようにしました。
次のように、いくつかの制約があります。
- .NET Core での作業に新しいプロバイダーが必要です
- SQL Server での空間サポートは有効になりません

## <a name="postponed-features"></a>延期された機能

EF Core 3.0 向けに最初に計画された一部の機能は、今後のリリースに延期されました。 

- 移行でモデルのパーツを無視する機能。[#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) によって追跡されます。
- 2 つの異なる問題によって追跡されるプロパティ バッグ エンティティ。共有型のエンティティに関する [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) と、インデックス付きプロパティのマッピング サポートに関する [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。

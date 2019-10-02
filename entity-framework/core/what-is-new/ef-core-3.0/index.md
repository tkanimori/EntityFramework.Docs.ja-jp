---
title: Entity Framework Core 3.0 の新機能
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ce53d0fa21acfd52dc5e8b37911202cab02f00c8
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813471"
---
# <a name="new-features-in-entity-framework-core-30"></a>Entity Framework Core 3.0 の新機能

以下のリストには、EF Core 3.0 の主な新機能が含まれています。

メジャー リリースである EF Core 3.0 には、いくつかの[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)も含まれています。これは、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化です。  

## <a name="linq-overhaul"></a>LINQ の見直し 

LINQ を使うと、任意の .NET 言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを提供できます。
しかし、LINQ では、任意の式 (メソッド呼び出しまたは操作) を含む複雑なクエリを無制限に記述できます。
これらの組み合わせをすべて処理する方法は、LINQ プロバイダーの主要な課題です。

EF Core 3.0 では、LINQ プロバイダーが再設計され、より多くのクエリ パターンの SQL への変換、より多くのケースでの効率的なクエリの生成、非効率的なクエリの未検出の防止が可能になりました。 新しい LINQ プロバイダーは、既存のアプリケーションやデータ プロバイダーを破壊することなく、今後のリリースで新しいクエリ機能やパフォーマンスの改善を提供できるようにするための基盤です。

### <a name="restricted-client-evaluation"></a>制限付きクライアント評価
最も重要な設計上の変更点は、パラメーターに変換できない、または SQL に変換できない LINQ 式の処理方法に関するものです。

以前のバージョンの EF Core では、SQL に変換できるクエリの部分を特定し、残りのクエリをクライアント上で実行していました。
この種のクライアント側の実行は、状況によっては望ましい場合がありますが、その他の多くのケースでは非効率的なクエリが発生する場合があります。

たとえば、EF Core 2.2 では、`Where()` 呼び出し内で述語を変換できなかった場合、フィルターなしで SQL ステートメントが実行され、すべての行がデータベースから転送された後、メモリ内でフィルター処理されます。

``` csharp
var specialCustomers = 
  context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

これは、データベースに少数の行しか含まれていない場合には許容できる可能性がありますが、データベースに多数の行が含まれている場合は、パフォーマンスに大きな問題が発生したり、アプリケーションに障害が発生したりする可能性があります。

EF Core 3.0 では、クライアント評価は最上位レベルのプロジェクション (基本的には `Select()` への最後の呼び出し) でのみ発生するように制限されました。
EF Core 3.0 で、クエリ内の他の場所では変換できない式が検出された場合、ランタイム例外がスローされます。

前の例のように述語の条件をクライアント上で評価するには、開発者がクエリの評価を LINQ to Objects に明示的に切り替えることが必要になりました。 

``` csharp
var specialCustomers =
  context.Customers
    .Where(c => c.Name.StartsWith(n)) 
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

これが既存のアプリケーションに与える可能性がある影響について詳しくは、[破壊的変更に関するドキュメント](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client)をご覧ください。

### <a name="single-sql-statement-per-linq-query"></a>LINQ クエリごとに 1 つの SQL ステートメント

3\.0 で大幅に変更された設計のもう 1 つの側面は、LINQ クエリごとに常に 1 つの SQL ステートメントが生成されるようになったことです。 以前のバージョンでは、コレクション ナビゲーション プロパティで `Include()` 呼び出しを変換する場合や、サブクエリを含む特定のパターンに従ったクエリを変換する場合など、場合によっては複数の SQL ステートメントが生成されていました。 これが便利なケースもいくつかあり、`Include()` の場合はネットワーク経由での冗長データの送信を回避するのにも役立ちました。しかし、実装が複雑になり、いくつかの非常に非効率的な動作 (N+1 クエリ) が発生し、複数のクエリ間で返されたデータに矛盾が生じる場合がある状況が発生しました。

クライアント評価と同様に、EF Core 3.0 で LINQ クエリを 1 つの SQL ステートメントに変換できなかった場合は、ランタイム例外がスローされます。 ただし、EF Core では、以前は複数のクエリが生成されていた一般的なパターンの多くを、JOIN を使って 1 つのクエリに変換できるようになっています。

## <a name="cosmos-db-support"></a>Cosmos DB のサポート 

EF Core 用の Cosmos DB プロバイダーでは、EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにします。 その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。 プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。

詳細については、[Cosmos DB プロバイダーに関するドキュメント](xref:core/providers/cosmos/index)をご覧ください。

## <a name="c-80-support"></a>C# 8.0 のサポート

EF Core 3.0 では、次のいくつかの [C# 8.0 の新機能](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8)が利用されています。

### <a name="asynchronous-streams"></a>非同期ストリーム

非同期クエリの結果は、新しい標準 `IAsyncEnumerable<T>` インターフェイスを使用して公開されるようになり、`await foreach` を使用して使用できるようになりました。

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
  Process(o);
} 
```

詳細については、[C# の非同期ストリームに関するドキュメント](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams)をご覧ください。

### <a name="nullable-reference-types"></a>null 許容参照型 

コード内でこの新しい機能を有効にした場合、EF Core によって参照型プロパティの null 値の許容が調べられ、データベース内の対応する列およびリレーションシップにそれが適用されます。null 非許容参照型のプロパティは、`[Required]` データ注釈属性が含まれているかのように処理されます。

たとえば、次のクラスでは、型 `string?` としてマークされたプロパティは省略可能として構成されますが、`string` は必須として構成されます。

``` csharp
public class Customer
{
  public int Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string? MiddleName { get; set; }
}
```

詳細については、EF Core ドキュメントの「[null 許容参照型の使用](xref:core/miscellaneous/nullable-reference-types)」をご覧ください。

## <a name="interception-of-database-operations"></a>データベース操作のインターセプト

EF Core 3.0 の新しいインターセプト API を使うと、EF Core の通常操作の一部として低レベルのデータベース操作が発生するたびに、カスタム ロジックの提供が自動的に呼び出されるようにすることができます。 たとえば、接続を開いたり、トランザクションをコミットしたり、コマンドを実行したりする場合です。 

EF 6 に存在していたインターセプト機能と同様に、インターセプターによって、操作の発生前または発生後にそれをインターセプトすることができます。 操作の発生前にインターセプトした場合は、実行を回避し、インターセプトのロジックから代わりの結果を渡すことができます。 

たとえば、コマンド テキストを操作するために、`IDbCommandInterceptor` を作成することができます。

``` csharp 
public class HintCommandInterceptor : DbCommandInterceptor
{
  public override InterceptionResult ReaderExecuting(
    DbCommand command, 
    CommandEventData eventData, 
    InterceptionResult result)
  {
    // Manipulate the command text, etc. here...
    command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
    return result;
  }
}
``` 

次に、 `DbContext` にこれを登録します。

``` csharp
services.AddDbContext(b => b
  .UseSqlServer(connectionString)
  .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>データベース ビューのリバース エンジニアリング

クエリ型 (データベースから読み取ることはできても更新は行えないデータを表す) は、[キーなしエンティティ型](xref:core/modeling/keyless-entity-types)に名前が変更されました。 それはほとんどのシナリオでデータベース ビューのマッピングに対して最適であるため、データベース ビューのリバース エンジニアリングを行う場合に、EF Core によってキーなしエンティティ型が自動的に作成されるようになりました。

たとえば、[dotnet ef コマンドライン ツール](xref:core/miscellaneous/cli/dotnet)を使って次のように入力します。

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

すると、ツールによって、キーなしのビューとテーブルの型が自動的にスキャフォールディングされます。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.Entity<Names>(entity =>
  {
    entity.HasNoKey();
    entity.ToView("Names");
  });

  modelBuilder.Entity<Things>(entity =>
  {
    entity.HasNoKey();
  });
}
```

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

これは EF Core 3.0 の機能ではありませんが、現在のお客様の多くにとって重要であると考えられます。 

多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると、多大な労力が必要となる場合があります。 そのため、.NET Core 3.0 上で実行できるように最新バージョンの EF 6 を移植することに決定しました。 

詳細については、「[EF 6 の新機能](xref:ef6/what-is-new/index)」をご覧ください。

## <a name="postponed-features"></a>延期された機能

EF Core 3.0 向けに最初に計画された一部の機能は、今後のリリースに延期されました。

- 移行でモデルの一部を無視する機能。[#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) として追跡されています。
- プロパティ バッグのエンティティ。2 つの異なるイシューとして追跡されています: 共有型のエンティティに関する [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) と、インデックス付きプロパティ マッピングのサポートに関する [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。

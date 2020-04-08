---
title: EF Core 3.0 での破壊的変更 - EF Core
author: ajcvickers
ms.date: 12/03/2019
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 6e0c17a22b56b206f18e47f678e3e237d5c42375
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413559"
---
# <a name="breaking-changes-included-in-ef-core-30"></a>EF Core 3.0 に含まれる破壊的変更

以下の API と動作変更により、3.0.0 へのアップグレード時に、既存のアプリケーションが中断される可能性があります。
データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](xref:core/providers/provider-log)に関するページに記載されています。

## <a name="summary"></a>まとめ

| **重大な変更**                                                                                               | **影響** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [LINQ クエリがクライアントで評価されなくなった](#linq-queries-are-no-longer-evaluated-on-the-client)         | 高       |
| [EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる](#netstandard21) | 高      |
| [EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった](#dotnet-ef) | 高      |
| [DetectChanges でストア生成キーの値が優先される](#dc) | 高      |
| [FromSql、ExecuteSql、および ExecuteSqlAsync の名前が変更された](#fromsql) | 高      |
| [クエリ型がエンティティ型と統合される](#qt) | 高      |
| [Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった](#no-longer) | Medium      |
| [既定で連鎖削除がすぐに行われるようになった](#cascade) | Medium      |
| [関連エンティティの一括読み込みが 1 つのクエリで行われるようになった](#eager-loading-single-query) | Medium      |
| [DeleteBehavior.Restrict のセマンティクスがクリーンになった](#deletebehavior) | Medium      |
| [所有型のリレーションシップ用の構成 API が変更された](#config) | Medium      |
| [各プロパティで独立したメモリ内整数キー生成が使用される](#each) | Medium      |
| [追跡なしのクエリでは、識別子の解決が実行されなくなった](#notrackingresolution) | Medium      |
| [メタデータ API の変更](#metadata-api-changes) | Medium      |
| [プロバイダー固有のメタデータ API の変更](#provider) | Medium      |
| [UseRowNumberForPaging が削除された](#urn) | Medium      |
| [FromSql メソッドをストアド プロシージャと共に使用して構成することができない](#fromsqlsproc) | Medium      |
| [FromSql メソッドはクエリのルートでのみ指定できる](#fromsql) | 低      |
| [~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます](#qe) | 低      |
| [一時キーの値がエンティティ インスタンスに設定されなくなった](#tkv) | 低      |
| [プリンシパルとテーブルを共有する依存エンティティが省略可能になった](#de) | 低      |
| [同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要がある](#aes) | 低      |
| [所有エンティティは、所有者がいないと、追跡クエリを使用してクエリを実行することができない](#owned-query) | 低      |
| [マップされていない型から継承されたプロパティが、すべての派生型の 1 つの列にマップされるようになった](#ip) | 低      |
| [外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった](#fkp) | 低      |
| [データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになった](#dbc) | 低      |
| [バッキング フィールドが既定で使用される](#backing-fields-are-used-by-default) | 低      |
| [複数の互換性があるバッキング フィールドが見つかった場合にスローされる](#throw-if-multiple-compatible-backing-fields-are-found) | 低      |
| [フィールド専用プロパティの名前はフィールドの名前に一致する必要がある](#field-only-property-names-should-match-the-field-name) | 低      |
| [AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなった](#adddbc) | 低      |
| [AddEntityFramework* により、サイズ制限がある IMemoryCache が追加される](#addentityframework-adds-imemorycache-with-a-size-limit) | 低      |
| [DbContext.Entry でローカルの DetectChanges が実行されるようになった](#dbe) | 低      |
| [文字列とバイト配列のキーが既定でクライアントによって生成されない](#string-and-byte-array-keys-are-not-client-generated-by-default) | 低      |
| [ILoggerFactory がスコープ サービスになった](#ilf) | 低      |
| [遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | 低      |
| [内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | 低      |
| [1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作](#nbh) | 低      |
| [いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更された](#rtnt) | 低      |
| [Relational:TypeMapping 注釈が単に TypeMapping となった](#rtt) | 低      |
| [派生型の ToTable で例外がスローされる](#totable-on-a-derived-type-throws-an-exception) | 低      |
| [EF Core で SQLite FK を適用するためのプラグマが送信されなくなった](#pragma) | 低      |
| [Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった](#sqlite3) | 低      |
| [GUID の値が SQLite にテキストとして格納されるようになった](#guid) | 低      |
| [Char の値が SQLite にテキストとして格納されるようになった](#char) | 低      |
| [移行 ID がインバリアント カルチャの暦を使用して生成されるようになった](#migid) | 低      |
| [IDbContextOptionsExtension から拡張機能の情報/メタデータが削除された](#xinfo) | 低      |
| [LogQueryPossibleExceptionWithAggregateOperator の名前が変更された](#lqpe) | 低      |
| [外部キー制約名の API が明確化された](#clarify) | 低      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync がパブリックに変更された](#irdc2) | 低      |
| [Microsoft.EntityFrameworkCore.Design が DevelopmentDependency パッケージになった](#dip) | 低      |
| [SQLitePCL.raw がバージョン 2.0.0 に更新された](#SQLitePCL) | 低      |
| [NetTopologySuite がバージョン 2.0.0 に更新された](#NetTopologySuite) | 低      |
| [System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される](#SqlClient) | 低      |
| [複数のあいまいな自己参照リレーションシップを構成する必要がある](#mersa) | 低      |
| [DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される](#udf-empty-string) | 低      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>LINQ クエリがクライアントで評価されなくなった

[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

**以前の動作**

3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。
既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。

**新しい動作**

3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。
クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。

**理由**

クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。
この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。
たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。
このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。
クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。

これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。

**軽減策**

クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a>EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる

[問題 #15498 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> EF Core 3.1 では .NET Standard 2.0 が再度ターゲットにされています。 このため、.NET Framework のサポートが復活します。

**以前の動作**

3\.0 以前の EF Core の場合は、.NET Standard 2.0 がターゲットとされ、その標準をサポートするすべてのプラットフォーム (.NET Framework を含む) 上で実行されていました。

**新しい動作**

3\.0 以降の EF Core の場合は、.NET Standard 2.1 がターゲットとされ、この標準をサポートするすべてのプラットフォームで実行されます。 これには、.NET Framework は含まれません。

**理由**

これは、.NET Core やその他の最新の .NET プラットフォーム (Xamarin など) にエネルギーを集中するために .NET テクノロジ全体にわたって行われる戦略的な決定の一部です。

**軽減策**

EF Core 3.1 を使用します。

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった

[問題 Announcements#325 の追跡](https://github.com/aspnet/Announcements/issues/325)

**以前の動作**

ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。

**新しい動作**

3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。

**理由**

この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。 また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。

この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。
また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。

**軽減策**

ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった

[問題 #14016 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

**以前の動作**

3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。 

**新しい動作**

3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。 

**理由**

この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。

**軽減策**

移行の管理や `DbContext` のスキャフォールディングを行えるようにするには、`dotnet-ef` をグローバル ツールとしてインストールします。

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更

[問題 #10996 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

**以前の動作**

EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。

**新しい動作**

EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。
次に例を示します。

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。
次に例を示します。

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。

**理由**

このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。
これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。

**軽減策**

新しいメソッド名を使用するように切り替えます。

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>FromSql メソッドをストアド プロシージャと共に使用して構成することができない

[問題 #15392 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

**以前の動作**

3\.0 EF Core より前では、FromSql メソッドにより、渡された SQL を構成できるかどうかの検出が試行されていました。 SQL がストアド プロシージャのように非コンポーザブルである場合、クライアント評価が行われていました。 次のクエリは、サーバー上でストアド プロシージャを実行し、クライアント側で FirstOrDefault を実行することで機能していました。

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

**新しい動作**

EF Core 3.0 以降では、SQL の解析は試行されません。 そのため、FromSqlRaw/FromSqlInterpolated の後に構成する場合、EF Core では、サブ クエリを実行することで SQL が構成されます。 したがって、構成でストアド プロシージャを使用している場合は、無効な SQL 構文の例外が発生します。

**理由**

EF Core 3.0 では、自動クライアント評価はサポートされていません。これは、[ここ](#linq-queries-are-no-longer-evaluated-on-the-client)で説明するように、エラーが発生しやすいためです。

**軽減策**

FromSqlRaw/FromSqlInterpolated でストアド プロシージャを使用している場合は、それを構成できないことがわかっているので、サーバー側での構成を回避するために、FromSql メソッド呼び出しの直後に __AsEnumerable/AsAsyncEnumerable__ を追加します。

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>FromSql メソッドはクエリのルートでのみ指定できる

[問題 #15704 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

**以前の動作**

EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。

**新しい動作**

EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。 他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。

**理由**

`FromSql` 以外の任意の場所で `DbSet` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。

**軽減策**

`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>追跡なしのクエリでは、識別子の解決が実行されなくなった

[問題 #13518 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

**以前の動作**

EF Core 3.0 以前の場合は、指定した型と ID を持つエンティティが出現するたびに同じエンティティ インスタンスが使用されます。 これは、追跡クエリの動作と一致します。 次のクエリを例にします。

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
指定したカテゴリに関連付けられている各 `Category` に対して同じ `Product` インスタンスが返されます。

**新しい動作**

EF Core 3.0 以降では、指定した型と ID を持つエンティティが、返されたグラフ内の異なる場所で検出されると、それぞれ異なるエンティティ インスタンスが作成されます。 たとえば、上記のクエリでは、2 つの製品が同じカテゴリに関連付けられている場合でも、各 `Category` に対して新しい `Product` インスタンスが返されるようになります。

**理由**

識別子の解決 (つまり、エンティティの型と ID が以前に検出されたエンティティと同じであることを確認する) を行う場合は、パフォーマンスおよびメモリのオーバーヘッドが増大します。 これは、通常、最初の場所で追跡なしのクエリが使用されている理由に矛盾しています。 また、識別子の解決は有用なこともありますが、エンティティをシリアル化してクライアントに送信する場合 (追跡なしのクエリでは一般的な処理) は必要ありません。

**軽減策**

識別子の解決が必要な場合は、追跡クエリを使用します。

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます

[問題 #14523 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

EF Core 3.0 の新しい構成では、あらゆるイベントのログ レベルをアプリケーションによって指定できるため、この変更は元に戻されます。 たとえば、SQL のログ記録を `Debug` に切り替えるには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>一時キーの値がエンティティ インスタンスに設定されなくなった

[問題 #12378 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

**以前の動作**

EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。
通常、これらの一時的な値は大きい負の数値でした。

**新しい動作**

3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。

**理由**

この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。 

**軽減策**

主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。
これは、次のようにして回避できます。
* ストア生成キーを使用しない。
* 外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。
* エンティティの追跡情報から実際の一時キーの値を取得する。
たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges でストア生成キーの値が優先される

[問題 #14616 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

**以前の動作**

EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。

**新しい動作**

EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。
つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。
キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。

**理由**

この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。

**軽減策**

エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。
この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。
たとえば、fluent API を使用する場合は、次のようになります。

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

データ注釈を使用する場合は、次のようになります。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a>既定で連鎖削除がすぐに行われるようになった

[問題 #10114 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

**以前の動作**

3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。

**新しい動作**

3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。
たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。

**理由**

この変更は、 _が呼び出される_前に`SaveChanges`どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。

**軽減策**

`context.ChangeTracker` の設定を使用して、以前の動作を復元することができます。
次に例を示します。

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>関連エンティティの一括読み込みが 1 つのクエリで行われるようになった

[問題 #18022 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

**以前の動作**

3\.0 より前では、`Include` 演算子を使用してコレクション ナビゲーションを一括で読み込むと、関連するエンティティ型ごとに 1 つずつ、リレーショナル データベースで複数のクエリが生成されていました。

**新しい動作**

3\.0 以降の EF Core では、リレーショナル データベースで結合を使用した単一のクエリが生成されます。

**理由**

単一の LINQ クエリを実装するために複数のクエリを実行すると、多くの問題が発生していました。これらの問題には、複数のデータベース ラウンドトリップが必要になった際のパフォーマンスの低下や、各クエリによってデータベースの異なる状態が観察された際のデータの一貫性の問題などが含まれます。

**軽減策**

技術的に、これは破壊的変更ではありませんが、コレクション ナビゲーションで単一のクエリに多数の `Include` 演算子が含まれている場合、アプリケーションのパフォーマンスに大きな影響を及ぼす可能性があります。 詳細情報、およびより効率的な方法でクエリを再記述する方法については、[こちらのコメントを参照してください](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085)。

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>DeleteBehavior.Restrict のセマンティクスがクリーンになりました

[問題 #12661 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

**以前の動作**

3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。

**新しい動作**

3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。

**理由**

この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。

**軽減策**

`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a>クエリ型がエンティティ型と統合される

[問題 #14194 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

**以前の動作**

EF Core 3.0 より前では、[クエリ型](xref:core/modeling/keyless-entity-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。
つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。

**新しい動作**

クエリ型は、現在、主キーなしの単なるエンティティ型になります。
キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。

**理由**

この変更は、クエリ型の目的に関する混乱を減らすために行われました。
つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。
同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。

**軽減策**

API の以下の部分は使用されなくなりました。
* **`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。
これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。
* **`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。
* **`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>所有型のリレーションシップ用の構成 API が変更された

[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

**以前の動作**

EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。 

**新しい動作**

EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。
次に例を示します。

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。
一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。
次に例を示します。

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。

**理由**

この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。
これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。

**軽減策**

上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>プリンシパルとテーブルを共有する依存エンティティが省略可能になりました

[問題 #9005 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

**以前の動作**

次のモデルがあるとします。
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `OrderDetails` を追加する場合には常に `Order` インスタンスが必要でした。


**新しい動作**

EF Core 3.0 以降では、`Order` なしで `OrderDetails` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。
`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。

**軽減策**

モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。 これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。

[問題 #14154 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

**以前の動作**

次のモデルがあるとします。
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。


**新しい動作**

EF Core 3.0 以降では、新しい `Version` 値が `Order` を所有している場合には `OrderDetails` に伝達されます。 それ以外の場合は、モデルの検証中に例外がスローされます。

**理由**

この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。

**軽減策**

テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。 シャドウ状態で作成することができます。
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a>所有エンティティは、所有者がいないと、追跡クエリを使用してクエリを実行することができない

[問題 #18876 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

**以前の動作**

EF Core 3.0 より前のバージョンでは、所有エンティティに対しても他のナビゲーションと同じようにクエリを実行することができました。

```csharp
context.People.Select(p => p.Address);
```

**新しい動作**

EF Core 3.0 以降では、追跡クエリで所有者のいない所有エンティティが提示されると、スローされます。

**理由**

所有エンティティは所有者なしでは操作できません。そのため、このようにクエリを実行するほとんどのケースでは、エラーになります。

**軽減策**

所有エンティティを後で変更するように追跡する必要がある場合は、所有者をクエリに含める必要があります。

それ以外の場合は、`AsNoTracking()` の呼び出しを追加します。

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました

[問題 #13998 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

**以前の動作**

次のモデルがあるとします。
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。

**新しい動作**

EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。

**理由**

以前の動作は予期しないものでした。

**軽減策**

プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった

[問題 #13274 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

**以前の動作**

次のモデルがあるとします。
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。
しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。

**新しい動作**

EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。
プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。
次に例を示します。

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**理由**

この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。

**軽減策**

プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました

[問題 #14218 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

**以前の動作**

EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

**新しい動作**

EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。

**理由**

この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。 新しい動作は、EF6 にも一致します。

**軽減策**

接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>各プロパティで独立したメモリ内整数キー生成が使用される

[問題 #6872 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

**以前の動作**

EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。

**新しい動作**

EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。
また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。

**理由**

この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。

**軽減策**

これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。
代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。

### <a name="backing-fields-are-used-by-default"></a>バッキング フィールドが既定で使用される

[問題 #12430 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

**以前の動作**

3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。
ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。

**新しい動作**

EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。
アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。

**理由**

この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。

**軽減策**

3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。
次に例を示します。

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>複数の互換性があるバッキング フィールドが見つかった場合にスローされる

[問題 #12523 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

**以前の動作**

EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。
これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。

**新しい動作**

EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。

**理由**

この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。

**軽減策**

あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。
たとえば、fluent API を使用する場合、次のようになります。

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>フィールド専用プロパティの名前はフィールドの名前に一致する必要があります

**以前の動作**

EF Core 3.0 以前では、プロパティは文字列値により指定できました。 .NET 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

**新しい動作**

EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

**理由**

この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。

**軽減策**

フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。
EF Core 3.0 以降の今後のリリースでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です (問題 [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307) を参照)。

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました

[問題 #14756 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

**以前の動作**

EF Core 3.0 より前のバージョンでは、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、DI を使ってログ記録とメモリ キャッシュ サービスも登録されました。

**新しい動作**

EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。

**理由**

EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。 ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。

**軽減策**

ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a>AddEntityFramework* により、サイズ制限がある IMemoryCache が追加される

[問題 #12905 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

**以前の動作**

EF Core 3.0 より前のバージョンでは、`AddEntityFramework*` メソッドを呼び出すと、サイズ制限のないメモリ キャッシュ サービスも DI に登録されていました。

**新しい動作**

EF Core 3.0 以降では、`AddEntityFramework*` によって IMemoryCache サービスがサイズ制限付きで登録されます。 その後に追加された他のサービスが IMemoryCache に依存している場合は、既定の制限にすぐに達して、例外やパフォーマンスの低下が発生する可能性があります。

**理由**

クエリ キャッシュ ロジックにバグがある場合、またはクエリが動的に生成される場合は、制限なしで IMemoryCache を使用すると、メモリ使用量が制御されなくなる可能性があります。 既定の制限を設定することで、DoS 攻撃の可能性を軽減できます。

**軽減策**

`AddEntityFramework*` または `AddDbContext` も呼び出された場合、ほとんどの場合、`AddDbContextPool` を呼び出す必要はありません。 したがって、最善の軽減策は、`AddEntityFramework*` の呼び出しを削除することです。

ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使用する前に、IMemoryCache の実装を DI コンテナーに明示的に登録します。

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry でローカルの DetectChanges が実行されるようになった

[問題 #13552 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

**以前の動作**

EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。
これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。

**新しい動作**

EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。
つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。

`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。

`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。

**理由**

この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。

**軽減策**

3\.0 より前の動作を確保するには、`ChangeTracker.DetectChanges()` を呼び出す前に明示的に `Entry` を呼び出します。

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>文字列とバイト配列のキーが既定でクライアントによって生成されない

[問題 #14617 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

**以前の動作**

EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。
このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。

**新しい動作**

EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。

**理由**

この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。

**軽減策**

3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。
たとえば、fluent API を使用する場合は、次のようになります。

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

データ注釈を使用する場合は、次のようになります。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory がスコープ サービスになった

[問題 #14698 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

**以前の動作**

EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。

**新しい動作**

EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。

**理由**

この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。

**軽減策**

この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。
これは一般的なことではありません。
このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。

このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった

[問題 #12780 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

**以前の動作**

EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。
プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。
このような場合、遅延読み込みを試みても操作なしとなります。

**新しい動作**

EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。
つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。
逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。
このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。

**理由**

この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。

**軽減策**

破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった

[問題 #10236 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

**以前の動作**

EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。

**新しい動作**

EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。 

**理由**

この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。

**軽減策**

このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。
しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。
次に例を示します。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作

[問題 #9171 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

**以前の動作**

EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。
次に例を示します。
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

このコードは、プライベートである可能性がある `Samurai` ナビゲーション プロパティを使って、`Entrance` を他のエンティティ型に関連付けているように見えます。

実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。

**新しい動作**

EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。

**理由**

以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。

**軽減策**

これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。
これは一般的ではありません。
前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。
次に例を示します。

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました

[問題 #15184 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

**以前の動作**

次の非同期メソッドでは、以前は `Task<T>` が返されていました。

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (および派生クラス)

**新しい動作**

前述のメソッドは、以前と同じ `ValueTask<T>` に対して `T` を返すようになりました。

**理由**

この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。

**軽減策**

上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。
より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `Task<T>` を呼び出すことによって `AsTask()` に変換する必要があります。
これにより、この変更による割り当ての削減が無効になることに注意してください。

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Relational:TypeMapping 注釈が単に TypeMapping となった

[問題 #9913 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

**以前の動作**

型マッピング注釈の注釈名は "Relational:TypeMapping" でした。

**新しい動作**

型マッピング注釈の注釈名は、"TypeMapping" となりました。

**理由**

型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。

**軽減策**

これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。
問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。

### <a name="totable-on-a-derived-type-throws-an-exception"></a>派生型の ToTable で例外がスローされる 

[問題 #11811 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

**以前の動作**

EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。 

**新しい動作**

EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。

**理由**

現在、派生型を別のテーブルにマップすることは有効ではありません。
この変更により、今後、これを行うことが有効になった場合に中断されなくなります。

**軽減策**

派生型を他のテーブルにマップしないようにします。

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex が HasIndex に置き換えられた 

[問題 #12366 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

**以前の動作**

EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。

**新しい動作**

EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。
`HasIndex().ForSqlServerInclude()`を使用します。

**理由**

この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。

**軽減策**

上記のように、新しい API を使用します。

### <a name="metadata-api-changes"></a>メタデータ API の変更点

[問題 #214 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**新しい動作**

次のプロパティは拡張メソッドに変換されました。

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

**理由**

この変更により、前述のインターフェイスの実装が簡単になります。

**軽減策**

新しい拡張メソッドを使用します。

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>プロバイダー固有のメタデータ API の変更

[問題 #214 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**新しい動作**

プロバイダー固有の拡張メソッドがフラット化されます。

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

**理由**

この変更により、前述の拡張メソッドの実装が簡単になります。

**軽減策**

新しい拡張メソッドを使用します。

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core で SQLite FK を適用するためのプラグマが送信されなくなった

[問題 #12151 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

**以前の動作**

EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。

**新しい動作**

EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。

**理由**

この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。

**軽減策**

外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。
それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった

**以前の動作**

EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。

**新しい動作**

EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。

**理由**

この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。

**軽減策**

iOS でネイティブの SQLite バージョンを使用するには、別の `Microsoft.Data.Sqlite` バンドルを使用するように `SQLitePCLRaw` を構成します。

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>GUID の値が SQLite にテキストとして格納されるようになった

[問題 #15078 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

**以前の動作**

GUID の値は、以前は SQLite に BLOB 値として格納されていました。

**新しい動作**

GUID の値はテキストとして格納されるようになりました。

**理由**

GUID のバイナリ形式は標準化されていません。 値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。

**軽減策**

次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Char の値が SQLite にテキストとして格納されるようになった

[問題 #15020 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

**以前の動作**

Char の値は、以前は SQLite に整数値として格納されていました。 たとえば、Char の値 *A* は整数値 65 として格納されていました。

**新しい動作**

Char の値はテキストとして格納されるようになりました。

**理由**

値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。

**軽減策**

次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>移行 ID がインバリアント カルチャの暦を使用して生成されるようになった

[問題 #12978 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

**以前の動作**

移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。

**新しい動作**

移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。

**理由**

データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。 インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。

**軽減策**

この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。 既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。

移行 ID は、移行のデザイナー ファイルの移行属性にあります。

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

移行履歴テーブルも更新する必要があります。

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>UseRowNumberForPaging の削除

[問題 #16400 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

**以前の動作**

EF Core 3.0 より前では、`UseRowNumberForPaging` を使って、SQL Server 2008 と互換性のあるページング用の SQL を生成することができました。

**新しい動作**

EF Core 3.0 以降では、EF によって生成できるのは、それ以降のバージョンの SQL Server とのみ互換性があるページング用の SQL のみとなります。 

**理由**

[SQL Server 2008 はもうサポートされていない製品であり](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/)、EF Core 3.0 で行われたクエリの変更に対応するようこの機能を更新する作業は負荷が大きいため、このような変更が行われます。

**軽減策**

生成された SQL がサポートされるように、より新しいバージョンの SQL Server に更新するか、より高い互換性レベルを使用することをお勧めします。 ただし、これを実行できない場合は、詳細情報とともに[問題の追跡にコメント](https://github.com/aspnet/EntityFrameworkCore/issues/16400)してください。 Microsoft では、フィードバックに基づいてこの決定を再検討する場合があります。

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>IDbContextOptionsExtension から拡張機能の情報/メタデータを削除

[問題 #16119 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

**以前の動作**

`IDbContextOptionsExtension` には、拡張機能に関するメタデータを提供するためのメソッドが含まれていました。

**新しい動作**

これらのメソッドは、新しい抽象基底クラス `DbContextOptionsExtensionInfo` 上に移動されました。これは新しい `IDbContextOptionsExtension.Info` プロパティから返されます。

**理由**

2\.0 から 3.0 までのリリースを通して、これらのメソッドに追加または変更を行う必要が複数回発生しました。
それらを抜き出して新しい抽象基底クラスに含めることで、既存の拡張機能を損なうことなく、簡単にこのような変更を加えられるようになります。

**軽減策**

新しいパターンに従うよう拡張機能を更新します。
その例は、EF Core のソース コードの、さまざまな種類の拡張機能に対する多数の `IDbContextOptionsExtension` の実装で見つかります。

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator の名前が変更された

[問題 #10985 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

**変更点**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。

**理由**

この警告イベントの名前が他のすべての警告イベントと照合されるためです。

**軽減策**

新しい名前を使用します。 (イベント ID 番号が変更されていないことを確認してください。)

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>外部キー制約名の API が明確化された

[問題 #10730 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

**以前の動作**

EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。 次に例を示します。

```csharp
var constraintName = myForeignKey.Name;
```

**新しい動作**

EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。 次に例を示します。

```csharp
var constraintName = myForeignKey.ConstraintName;
```

**理由**

この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。

**軽減策**

新しい名前を使用します。

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更

[問題 #15997 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

**以前の動作**

EF Core 3.0 より前では、これらのメソッドは保護されていました。

**新しい動作**

EF Core 3.0 以降、これらのメソッドはパブリックになります。

**理由**

これらのメソッドは、作成されたデータベースが空であるかどうかを判断するために EF によって使用されます。 これは、EF の外部から、移行を適用するかどうか判断する場合にも役立ちます。

**軽減策**

すべてのオーバーライドのアクセシビリティを変更します。

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更

[問題 #11506 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

**以前の動作**

EF Core 3.0 より前では、Microsoft.EntityFrameworkCore.Design は通常の NuGet パッケージであり、それに依存していたプロジェクトによってそのアセンブリを参照できました。

**新しい動作**

EF Core 3.0 以降、これは DevelopmentDependency パッケージになります。 つまり、依存関係が他のプロジェクトに推移的にフローすることがなくなり、規定ではそのアセンブリを参照できなくなります。

**理由**

このパッケージは、デザイン時に使用されることだけを想定しています。 デプロイされたアプリケーションからこれを参照すべきではありません。 パッケージを DevelopmentDependency にすることで、この推奨事項が強化されます。

**軽減策**

EF Core のデザイン時の動作をオーバーライドするためにこのパッケージを参照する必要がある場合は、プロジェクトの PackageReference 項目メタデータを更新することができます。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

このパッケージが Microsoft.EntityFrameworkCore.Tools 経由で推移的に参照されている場合は、パッケージに明示的な PackageReference を追加して、そのメタデータを変更する必要があります。 このような明示的な参照は、パッケージの型が必要なプロジェクトに追加する必要があります。

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL.raw のバージョン 2.0.0 への更新

[問題 #14824 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

**以前の動作**

Microsoft.EntityFrameworkCore.Sqlite は、以前はバージョン 1.1.12 の SQLitePCL.raw に依存していました。

**新しい動作**

バージョン 2.0.0 に依存するようパッケージが更新されました。

**理由**

バージョン 2.0.0 の SQLitePCL.raw では、.NET Standard 2.0 をターゲットとします。 これは以前は、推移的なパッケージの大規模なクロージャの動作を必要とする .NET Standard 1.1 をターゲットとしていました。

**軽減策**

SQLitePCL.raw バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。 詳細については、[リリース ノート](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)をご覧ください。

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite のバージョン 2.0.0 への更新

[問題 #14825 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

**以前の動作**

以前の空間パッケージはバージョン 1.15.1 の NetTopologySuite に依存していました。

**新しい動作**

バージョン 2.0.0 に依存するようパッケージが更新されました。

**理由**

バージョン 2.0.0 の NetTopologySuite は、EF Core の使用に伴ういくつかのユーザビリティの問題に対処することを目的としています。

**軽減策**

NetTopologySuite バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。 詳細については、[リリース ノート](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001)をご覧ください。

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される

[問題 #15636 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

**以前の動作**

Microsoft.EntityFrameworkCore.SqlServer は、以前は System.Data.SqlClient に依存していました。

**新しい動作**

Microsoft.Data.SqlClient に依存するようにパッケージを更新しました。

**理由**

Microsoft.Data.SqlClient は今後の SQL Server の主力データ アクセス ドライバーであり、System.Data.SqlClient は開発の中心ではなくなりました。
Always Encrypted などの重要な機能は、Microsoft.Data.SqlClient でのみ使用できます。

**軽減策**

コードが System.Data.SqlClient に直接依存している場合は、代わりに Microsoft.Data.SqlClient を参照するように変更する必要があります。2 つのパッケージは非常に高度な API 互換性を維持しているため、これは単純なパッケージと名前空間の変更のみである必要があります。

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>複数のあいまいな自己参照リレーションシップを構成する必要がある 

[イシュー #13573 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

**以前の動作**

複数の自己参照型一方向ナビゲーション プロパティと一致する FK を持つエンティティ型が、1 つのリレーションシップとして正しく構成されませんでした。 次に例を示します。

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

**新しい動作**

このシナリオがモデル構築で検出されるようになり、モデルがあいまいであることを示す例外がスローされます。

**理由**

結果として得られるモデルはあいまいであり、通常、この場合には間違っている可能性があるためです。

**軽減策**

完全に構成されたリレーションシップを使用します。 次に例を示します。

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される

[問題 #12757 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

**以前の動作**

空の文字列としてスキーマを使用して構成された DbFunction は、スキーマのない組み込み関数として扱われていました。 たとえば、次のコードは `DatePart` CLR 関数を SqlServer の組み込み関数 `DATEPART` にマップします。

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

**新しい動作**

すべての DbFunction マッピングは、ユーザー定義関数にマップされるものと見なされます。 そのため、空の文字列値を指定すると、関数がモデルの既定のスキーマ内に配置されます。 それ以外の、スキーマとなり得るものは fluent API の `modelBuilder.HasDefaultSchema()` または `dbo` を使用して明示的に構成されます。

**理由**

以前は空のスキーマを使用して関数を組み込みとして扱っていましたが、そのロジックは、組み込みの関数がどのスキーマにも属していない SqlServer にのみ適用されます。

**軽減策**

DbFunction の変換を手動で組み込み関数にマップされるように構成します。

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: fd593b2832a5a6ffe27cd4493127b5d405f684ba
ms.sourcegitcommit: ce44f85a5bce32ef2d3d09b7682108d3473511b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58914128"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更

> [!IMPORTANT]
> 機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。

以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。
データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。
ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>LINQ クエリがクライアントで評価されなくなった

[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

3.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。
既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。

**新しい動作**

3.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。
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

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった

[問題 Announcements#325 の追跡](https://github.com/aspnet/Announcements/issues/325)

この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されました。 

**以前の動作**

ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。

**新しい動作**

3.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。

**理由**

この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。 また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。

この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。
また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。

**軽減策**

ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更

[問題 #10996 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**以前の動作**

EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。

**新しい動作**

EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。
次に例を示します。

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。
次に例を示します。

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。

**理由**

このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。
これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。

**軽減策**

新しいメソッド名を使用するように切り替えます。

## <a name="query-execution-is-logged-at-debug-level"></a>クエリの実行がデバッグ レベルでログに記録される

[問題 #14523 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、クエリと他のコマンドの実行は、`Info` レベルでログに記録されました。

**新しい動作**

EF Core 3.0 以降では、コマンド/SQL の実行は、`Debug` レベルでログに記録されます。

**理由**

この変更は、`Info` ログ レベルでのノイズを減らすために行われました。

**軽減策**

このログ イベントは、イベント ID 20100 の `RelationalEventId.CommandExecuting` によって定義されています。
`Info` レベルで SQL をもう一度ログに記録するには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。
次に例を示します。
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>一時キーの値がエンティティ インスタンスに設定されなくなった

[問題 #12378 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

この変更は、EF Core 3.0 プレビュー 2 で導入されました。

**以前の動作**

EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。
通常、これらの一時的な値は大きい負の数値でした。

**新しい動作**

3.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。

**理由**

この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。 

**軽減策**

主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。
これは、次のようにして回避できます。
* ストア生成キーを使用しない。
* 外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。
* エンティティの追跡情報から実際の一時キーの値を取得する。
たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。

## <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges でストア生成キーの値が優先される

[問題 #14616 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

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

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

データ注釈を使用する場合は、次のようになります。

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a>既定で連鎖削除がすぐに行われるようになった

[問題 #10114 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

3.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。

**新しい動作**

3.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。
たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。

**理由**

この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。

**軽減策**

`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。
次に例を示します。

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a>クエリ型がエンティティ型と統合される

[問題 #14194 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。
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

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a>所有型のリレーションシップ用の構成 API が変更された

[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。 

**新しい動作**

EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。
次に例を示します。

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。
一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。
次に例を示します。

```C#
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

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>プリンシパルとテーブルを共有する依存エンティティが省略可能になりました

[問題 #9005 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

次のモデルがあるとします。
```C#
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
EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。


**新しい動作**

EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。
`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。

**軽減策**

モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。 これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。

[問題 #14154 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

次のモデルがあるとします。
```C#
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

EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。 それ以外の場合は、モデルの検証中に例外がスローされます。

**理由**

この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。

**軽減策**

テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。 シャドウ状態で作成することができます。
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました

[問題 #13998 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

次のモデルがあるとします。
```C#
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

```C#
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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった

[問題 #13274 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

次のモデルがあるとします。
```C#
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

```C#
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

```C#
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

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました

[問題 #14218 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。

```C#
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

この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。 新しい動作は、EF6 とほぼ一致します。

**軽減策**

接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。

```C#
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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>各プロパティで独立したメモリ内整数キー生成が使用される

[問題 #6872 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

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

## <a name="backing-fields-are-used-by-default"></a>バッキング フィールドが既定で使用される

[問題 #12430 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

この変更は、EF Core 3.0 プレビュー 2 で導入されました。

**以前の動作**

3.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。
ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。

**新しい動作**

EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常にそのプロパティの読み取りと書き込みが行われます。
アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。

**理由**

この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。

**軽減策**

3.0 より前の動作は、modelBuilder fluent API でプロパティ アクセス モードの構成を使用して、復元できます。
次に例を示します。

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>複数の互換性があるバッキング フィールドが見つかった場合にスローされる

[問題 #12523 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

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

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました

[問題 #14756 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。

**新しい動作**

EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。

**理由**

EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。 ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。

**軽減策**

ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry でローカルの DetectChanges が実行されるようになった

[問題 #13552 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

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

3.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>文字列とバイト配列のキーが既定でクライアントによって生成されない

[問題 #14617 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。
このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。

**新しい動作**

EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。

**理由**

この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。

**軽減策**

3.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。
たとえば、fluent API を使用する場合は、次のようになります。

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

データ注釈を使用する場合は、次のようになります。

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory がスコープ サービスになった

[問題 #14698 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

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

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a>IDbContextOptionsExtension に IDbContextOptionsExtensionWithDebugInfo が結合される

[問題 #13552 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

`IDbContextOptionsExtensionWithDebugInfo` は、2.x リリース サイクル時のインターフェイスへの破壊的変更を回避するために、`IDbContextOptionsExtension` から拡張された追加のオプションのインターフェイスです。

**新しい動作**

インターフェイスはまとめて `IDbContextOptionsExtension` に結合されるようになりました。

**理由**

この変更は、インターフェイスが概念的には 1 つであるため、行われました。

**軽減策**

`IDbContextOptionsExtension` のすべての実装を、新しいメンバーをサポートするように更新する必要があります。

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった

[問題 #12780 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

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

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった

[問題 #10236 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

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

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作

[問題 #9171 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` を呼び出すコードは、わかりにくい方法で解釈されていました。
次に例を示します。
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。

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

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました

[問題 #15184 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

この変更は、EF Core 3.0 プレビュー 4 で導入されます。

**以前の動作**

次の非同期メソッドでは、以前は `Task<T>` が返されていました。

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (および派生クラス)

**新しい動作**

前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。

**理由**

この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。

**軽減策**

上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。
より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。
これにより、この変更による割り当ての削減が無効になることに注意してください。

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Relational:TypeMapping 注釈が単に TypeMapping となった

[問題 #9913 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

この変更は、EF Core 3.0 プレビュー 2 で導入されました。

**以前の動作**

型マッピング注釈の注釈名は "Relational:TypeMapping" でした。

**新しい動作**

型マッピング注釈の注釈名は、"TypeMapping" となりました。

**理由**

型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。

**軽減策**

これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。
問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。

## <a name="totable-on-a-derived-type-throws-an-exception"></a>派生型の ToTable で例外がスローされる 

[問題 #11811 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。 

**新しい動作**

EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。

**理由**

現在、派生型を別のテーブルにマップすることは有効ではありません。
この変更により、今後、これを行うことが有効になった場合に中断されなくなります。

**軽減策**

派生型を他のテーブルにマップしないようにします。

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex が HasIndex に置き換えられた 

[問題 #12366 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。

**新しい動作**

EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。
`HasIndex().ForSqlServerInclude()` を使用してください。

**理由**

この変更は、すべてのデータ プロバイダーのために `Includes` でインデックス用の API を 1 か所に統合するために行われました。

**軽減策**

上記のように、新しい API を使用します。

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core で SQLite FK を適用するためのプラグマが送信されなくなった

[問題 #12151 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

この変更は、EF Core 3.0 プレビュー 3 で導入されました。

**以前の動作**

EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。

**新しい動作**

EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。

**理由**

この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。

**軽減策**

外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。
それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった

**以前の動作**

EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。

**新しい動作**

EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。

**理由**

この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。

**軽減策**

iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>GUID の値が SQLite にテキストとして格納されるようになった

[問題 #15078 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**以前の動作**

GUID の値は、以前は SQLite に BLOB 値として格納されていました。

**新しい動作**

GUID の値はテキストとして格納されます。

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

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Char の値が SQLite にテキストとして格納されるようになった

[問題 #15020 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**以前の動作**

Char の値は、以前は SQLite に整数値として格納されていました。 たとえば、Char の値 *A* は整数値 65 として格納されていました。

**新しい動作**

Char の値はテキストとして格納されます。

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

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>移行 ID がインバリアント カルチャの暦を使用して生成されるようになった

[問題 #12978 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**以前の動作**

移行 ID は選択されているカルチャの暦を使用して意図なく生成されていました。

**新しい動作**

移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。

**理由**

データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。 インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。

**軽減策**

この変更は、グレゴリオ暦以外の暦 (タイ仏暦など) を使用し、1 年がグレゴリオ暦より長くなるすべてのユーザーに影響します。 既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。

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

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator の名前が変更された

[問題 #10985 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**変更**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。

**理由**

この警告イベントの名前が他のすべての警告イベントと照合されるためです。

**軽減策**

新しい名前を使用します。 (イベント ID 番号が変更されていないことを確認してください。)

## <a name="clarify-api-for-foreign-key-constraint-names"></a>外部キー制約名の API が明確化された

[問題 #10730 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

この変更は、EF Core 3.0 プレビュー 4 で導入されました。

**以前の動作**

EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。 次に例を示します。

```C#
var constraintName = myForeignKey.Name;
```

**新しい動作**

EF Core 3.0 以降は、外部キー制約名が "定数名" と呼ばれるようになりました。 次に例を示します。

```C#
var constraintName = myForeignKey.ConstraintName;
```

**理由**

この変更により、この領域の名前付けに一貫性が生まれ、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。

**軽減策**

新しい名前を使用します。

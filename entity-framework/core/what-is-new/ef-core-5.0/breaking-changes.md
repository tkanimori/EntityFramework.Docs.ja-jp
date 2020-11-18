---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e2537dbc1d5dba48450bd0fea7712054ba2fa622
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503177"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 での破壊的変更

次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。

## <a name="summary"></a>まとめ

| **重大な変更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります](#required-dependent)                                 | 中間     |
| [クエリの定義は、プロバイダー固有のメソッドに置き換えられます](#defining-query)                                                          | 中間     |
| [非 null 参照ナビゲーションがクエリによって上書きされません](#nonnullreferences)                                                   | 中間     |
| [SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました](#geometric-sqlite)                                                   | 低        |
| [Cosmos: パーティション キーが主キーに追加されるようになりました](#cosmos-partition-key)                                                        | 低        |
| [Cosmos: `id` プロパティの名前が `__id` に変更されました](#cosmos-id)                                                                                 | 低        |
| [Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)                                             | 低        |
| [Cosmos: GetPropertyName と SetPropertyName の名前が変更されました](#cosmos-metadata)                                                          | 低        |
| [エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます](#non-added-generation) | 低        |
| [IMigrationsModelDiffer で IRelationalModel が使用されるようになりました](#relational-model)                                                                 | Low        |
| [識別子が読み取り専用です](#read-only-discriminators)                                                                             | Low        |
| [プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます](#no-client-methods)                                              | 低        |
| [IndexBuilder.HasName が古い形式に](#index-obsolete)                                                                               | Low        |
| [リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように](#pluralizer)                                                 | Low        |
| [スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます](#inavigationbase)                                     | 低        |
| [相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました](#collection-distinct-groupby) | 低        |
| [プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません](#queryable-projection)                                          | 低        |

## <a name="medium-impact-changes"></a>影響が中程度の変更

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります

[問題 #17286 の追跡](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>以前の動作

必要に応じて、プリンシパルへのナビゲーションのみを構成できます。 したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。

#### <a name="new-behavior"></a>新しい動作

必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。

依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>理由

必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。

#### <a name="mitigations"></a>軽減策

依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>クエリの定義は、プロバイダー固有のメソッドに置き換えられます

[問題 #18903 の追跡](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>以前の動作

エンティティ型は、コア レベルでクエリを定義するためにマップされました。 エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。

#### <a name="new-behavior"></a>新しい動作

クエリを定義するための API は非推奨とされます。 新しいプロバイダー固有の API が導入されました。

#### <a name="why"></a>理由

クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。

- クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。
- リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。

最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。 このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。 その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。 そのため、概念を簡略化することにしました。 LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。 詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。

#### <a name="mitigations"></a>軽減策

リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。
メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>非 null 参照ナビゲーションがクエリによって上書きされません

[イシュー #2693 の追跡](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>以前の動作

EF Core 3.1 では、非 null 値に意図的に初期化された参照ナビゲーションが、キー値が一致するかどうかに関係なく、データベースからのエンティティ インスタンスによって上書きされることがあります。 ただし、それ以外の場合は逆になり、EF Core 3.1 で既存の非 null 値のままになります。

#### <a name="new-behavior"></a>新しい動作

EF Core 5.0 以降では、非 null 参照ナビゲーションは、クエリから返されたインスタンスによって上書きされることはありません。

"_コレクション_" ナビゲーションの空のコレクションへの意図的な初期化は、引き続きサポートされていることに注意してください。

#### <a name="why"></a>理由

参照ナビゲーション プロパティを "空の" エンティティ インスタンスに初期化すると、あいまいな状態になります。 たとえば、次のように入力します。

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

通常、ブログと作成者のクエリにおいては、最初に `Blog` インスタンスを作成した後、データベースから返されたデータに基づいて適切な `Author` インスタンスを設定します。 ただし、この場合、`Blog.Author` のすべてのプロパティは、空の `Author` に既に初期化されています。 ただし、EF Core には、このインスタンスが "空" であることを認識する方法がありません。 そのため、このインスタンスを上書きすると、有効な `Author` が暗黙的に破棄される可能性があります。 したがって、EF Core 5.0 では、既に初期化されているナビゲーションは常に上書きされないようになりました。

この新しい動作は、EF6 の動作ともほとんどの場合は一致しますが、調査において EF6 と一致しない場合がいくつか見つかっています。  

#### <a name="mitigations"></a>軽減策

この断絶が発生した場合の修正方法は、参照ナビゲーション プロパティの意図的な初期化を止めることです。

## <a name="low-impact-changes"></a>影響が少ない変更

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました

[イシュー #14257 の追跡](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>以前の動作

HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。 ただし、この影響を受けていたのはデータベースの作成だけです。 追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。 また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/dotnet/efcore/issues/14257) を参照してください)。

#### <a name="new-behavior"></a>新しい動作

追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。 この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。

#### <a name="why"></a>理由

列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。

#### <a name="mitigations"></a>軽減策

`HasColumnType` を使用してディメンションを指定します。

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: パーティション キーが主キーに追加されるようになりました

[問題 #15289 の追跡](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>以前の動作

パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。

#### <a name="new-behavior"></a>新しい動作

規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。

#### <a name="why"></a>理由

この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。

#### <a name="mitigations"></a>軽減策

パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: `id` プロパティの名前が `__id` に変更されました

[問題 #17751 の追跡](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>以前の動作

`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。

#### <a name="new-behavior"></a>新しい動作

規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。

#### <a name="why"></a>理由

この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。

#### <a name="mitigations"></a>軽減策

3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました

[問題 #17306 の追跡](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>以前の動作

byte[] 型のプロパティは、数値配列として格納されていました。

#### <a name="new-behavior"></a>新しい動作

byte[] 型のプロパティは、base64 文字列として格納されるようになりました。

#### <a name="why"></a>理由

この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。

#### <a name="mitigations"></a>軽減策

数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。 この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/dotnet/efcore/issues/17306)にコメントしてください。

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName と SetPropertyName の名前が変更されました

[問題 #17874 の追跡](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>以前の動作

以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました

#### <a name="new-behavior"></a>新しい動作

古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました

#### <a name="why"></a>理由

この変更により、これらのメソッドの構成に関するあいまいさが解消されます。

#### <a name="mitigations"></a>軽減策

新しい API を使用します。

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます

[問題 #15289 の追跡](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>以前の動作

値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。

#### <a name="new-behavior"></a>新しい動作

値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。

#### <a name="why"></a>理由

この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。

#### <a name="mitigations"></a>軽減策

値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer で IRelationalModel が使用されるようになりました

[問題 #20305 の追跡](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>以前の動作

`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。

#### <a name="new-behavior"></a>新しい動作

`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。 ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。

#### <a name="why"></a>理由

`IRelationalModel` は、データベース スキーマの新しく追加された表現です。 違いを見つけるためにこれを使用する方が高速で正確です。

#### <a name="mitigations"></a>軽減策

次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>識別子が読み取り専用です

[問題 #21154 の追跡](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>以前の動作

`SaveChanges` を呼び出す前に識別子の値を変更することができました。

#### <a name="new-behavior"></a>新しい動作

上記のケースでは、例外がスローされます。

#### <a name="why"></a>理由

EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。

#### <a name="mitigations"></a>軽減策

識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます

[問題 #20294 の追跡](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>以前の動作

プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。 たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。

#### <a name="new-behavior"></a>新しい動作

プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。

#### <a name="why"></a>理由

プロバイダー固有のメソッドは、データベース関数にマップされます。 マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。 クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。 これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。 InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。 InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。

#### <a name="mitigations"></a>軽減策

データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder.HasName が古い形式に

[問題 #21089 の追跡](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>以前の動作

以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。 インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。

#### <a name="new-behavior"></a>新しい動作

同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。 これらのインデックスは、モデル内の名前で区別されるようになりました。 慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。

#### <a name="why"></a>理由

Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。 この変更により、その方向に新たな一歩を踏み出すことができます。

#### <a name="mitigations"></a>軽減策

以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。

EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています

[問題 #11160 の追跡](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>以前の動作

以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。

#### <a name="new-behavior"></a>新しい動作

EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。 これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。

#### <a name="why"></a>理由

コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。

#### <a name="mitigations"></a>軽減策

プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます

[イシュー #2568 の追跡](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>以前の動作

5\.0 より前の EF Core では、`INavigation` インターフェイスによって表される 1 つの形式のナビゲーション プロパティのみがサポートされていました。

#### <a name="new-behavior"></a>新しい動作

EF Core 5.0 では、"スキップ ナビゲーション" を使用する多対多のリレーションシップが導入されています。 これらは `ISkipNavigation` インターフェイスによって表され、`INavigation` のほとんどの機能は、共通の基底インターフェイスである `INavigationBase` に移されています。

#### <a name="why"></a>理由

通常のナビゲーションとスキップ ナビゲーションの機能のほとんどは同じです。 ただし、関連する外部キーはリレーションシップのどちらの端にも直接は存在せず、結合エンティティ内にあるため、スキップ ナビゲーションでの FK へのリレーションシップは通常のナビゲーションとは異なります。

#### <a name="mitigations"></a>軽減策

多くの場合は、他の変更なしで新しい基底インターフェイスを使用するようにアプリケーションを切り替えることができます。 ただし、外部キー プロパティへのアクセスにナビゲーションが使用されている場合は、アプリケーションのコードを、通常のナビゲーションのみに制限するか、通常とスキップの両方のナビゲーションで適切な処理を行うように更新する必要があります。

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました

[イシュー #15873 の追跡](https://github.com/dotnet/efcore/issues/15873)

**以前の動作**

以前は、相関コレクションに続いて `GroupBy` が含まれるクエリおよび `Distinct` を使用する一部のクエリは、実行が許可されていました。

GroupBy の例:

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

`Distinct` の例 - 具体的には、内部コレクション プロジェクションに主キーが含まれていない `Distinct` クエリ:

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

これらのクエリは、内部コレクションに重複が含まれている場合は正しくない結果を返す可能性がありましたが、内部コレクション内のすべての要素が一意である場合は正しく動作しました。

**新しい動作**

これらのクエリはサポートされなくなりました。 結果を正しく作成するための十分な情報がないことを示す例外がスローされます。

**理由**

相関コレクションのシナリオの場合、正しい親にコレクション エンティティを割り当てるには、エンティティの主キーがわかっている必要があります。 内部コレクションで `GroupBy` または `Distinct` が使用されていない場合は、足りない主キーをプロジェクションに単に追加できます。 ただし、`GroupBy` と `Distinct` の場合は、`GroupBy` または `Distinct` の操作の結果が変わってしまうため実行できません。

**軽減策**

内部コレクションで `GroupBy` または `Distinct` 操作を使用しないようにクエリを書き直し、クライアントでこれらの操作を代わりに実行します。

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません

[イシュー #16314 の追跡](https://github.com/dotnet/efcore/issues/16314)

**以前の動作**

以前は、たとえば `List<T>` コンストラクターへの引数のような一部の場合には、プロジェクションの内部でクエリ可能型のコレクションを使用できました。

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**新しい動作**

これらのクエリはサポートされなくなりました。 クエリ可能型のオブジェクトを作成できないことが示され、これを修正する方法が提案されている、例外がスローされます。

**理由**

クエリ可能型のオブジェクトを具体化することはできないので、それらは代わりに `List<T>` 型を使用して自動的に作成されます。 このため、型の不一致による例外が発生する場合がよくあります。これはわかりにくいものであり、ユーザーが驚くことがあります。 パターンを認識し、より意味のある例外をスローすることにしました。

**軽減策**

プロジェクション内でクエリ可能オブジェクトの後に `ToList()` の呼び出しを追加します。

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```

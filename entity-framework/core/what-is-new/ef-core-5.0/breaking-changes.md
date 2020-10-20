---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065642"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 での破壊的変更

次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。

## <a name="summary"></a>まとめ

| **重大な変更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります](#required-dependent)                                 | 中間     |
| [クエリの定義は、プロバイダー固有のメソッドに置き換えられます](#defining-query)                                                          | 中間     |
| [SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました](#geometric-sqlite)                                                   | Low        |
| [Cosmos: パーティション キーが主キーに追加されるようになりました](#cosmos-partition-key)                                                        | 低        |
| [Cosmos: `id` プロパティの名前が `__id` に変更されました](#cosmos-id)                                                                                 | 低        |
| [Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)                                             | 低        |
| [Cosmos: GetPropertyName と SetPropertyName の名前が変更されました](#cosmos-metadata)                                                          | 低        |
| [エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます](#non-added-generation) | 低        |
| [IMigrationsModelDiffer で IRelationalModel が使用されるようになりました](#relational-model)                                                                 | 低        |
| [識別子が読み取り専用です](#read-only-discriminators)                                                                             | Low        |
| [プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます](#no-client-methods)                                              | Low        |
| [IndexBuilder.HasName が古い形式に](#index-obsolete)                                                                               | 低        |
| [リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように](#pluralizer)                                                 | Low        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました

[イシュー #14257 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**以前の動作**

HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。 ただし、この影響を受けていたのはデータベースの作成だけです。 追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。 また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。

**新しい動作**

追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。 この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。

**理由**

列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。

**軽減策**

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります

[問題 #17286 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**以前の動作**

必要に応じて、プリンシパルへのナビゲーションのみを構成できます。 したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。

**新しい動作**

必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。

依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**理由**

必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。

**軽減策**

依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: パーティション キーが主キーに追加されるようになりました

[問題 #15289 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**以前の動作**

パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。

**新しい動作**

規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。

**理由**

この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。

**軽減策**

パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: `id` プロパティの名前が `__id` に変更されました

[問題 #17751 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**以前の動作**

`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。

**新しい動作**

規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。

**理由**

この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。

**軽減策**

3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました

[問題 #17306 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**以前の動作**

byte[] 型のプロパティは、数値配列として格納されていました。

**新しい動作**

byte[] 型のプロパティは、base64 文字列として格納されるようになりました。

**理由**

この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。

**軽減策**

数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。 この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)にコメントしてください。

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName と SetPropertyName の名前が変更されました

[問題 #17874 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**以前の動作**

以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました

**新しい動作**

古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました

**理由**

この変更により、これらのメソッドの構成に関するあいまいさが解消されます。

**軽減策**

新しい API を使用します。

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます

[問題 #15289 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**以前の動作**

値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。

**新しい動作**

値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。

**理由**

この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。

**軽減策**

値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer で IRelationalModel が使用されるようになりました

[問題 #20305 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**以前の動作**

`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。

**新しい動作**

`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。 ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。

**理由**

`IRelationalModel` は、データベース スキーマの新しく追加された表現です。 違いを見つけるためにこれを使用する方が高速で正確です。

**軽減策**

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

[問題 #21154 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**以前の動作**

`SaveChanges` を呼び出す前に識別子の値を変更することができました。

**新しい動作**

上記のケースでは、例外がスローされます。

**理由**

EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。

**軽減策**

識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>クエリの定義は、プロバイダー固有のメソッドに置き換えられます

[問題 #18903 の追跡](https://github.com/dotnet/efcore/issues/18903)

**以前の動作**

エンティティ型は、コア レベルでクエリを定義するためにマップされました。 エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。

**新しい動作**

クエリを定義するための API は非推奨とされます。 新しいプロバイダー固有の API が導入されました。

**理由**

クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。

- クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。
- リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。

最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。 このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。 その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。 そのため、概念を簡略化することにしました。 LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。 詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。

**軽減策**

リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。
メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます

[問題 #20294 の追跡](https://github.com/dotnet/efcore/issues/20294)

**以前の動作**

プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。 たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。

**新しい動作**

プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。

**理由**

プロバイダー固有のメソッドは、データベース関数にマップされます。 マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。 クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。 これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。 InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。 InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。

**軽減策**

データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder.HasName が古い形式に

[問題 #21089 の追跡](https://github.com/dotnet/efcore/issues/21089)

**以前の動作**

以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。 インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。

**新しい動作**

同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。 これらのインデックスは、モデル内の名前で区別されるようになりました。 慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。

**理由**

Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。 この変更により、その方向に新たな一歩を踏み出すことができます。

**軽減策**

以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。

EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように

[問題 #11160 の追跡](https://github.com/dotnet/efcore/issues/11160)

**以前の動作**

以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。

**新しい動作**

EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。 これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。

**理由**

コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。

**軽減策**

プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。

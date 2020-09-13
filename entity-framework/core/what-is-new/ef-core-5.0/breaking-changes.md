---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618679"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 での破壊的変更

次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。

## <a name="summary"></a>まとめ

| **重大な変更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります](#required-dependent)                                 | 中間     |
| [SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました](#geometric-sqlite)                                                   | Low        |
| [エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます](#non-added-generation)  | 低        |
| [IMigrationsModelDiffer で IRelationalModel が使用されるようになりました](#relational-model)                                                                 | 低        |
| [識別子が読み取り専用です](#read-only-discriminators)                                                                             | Low        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました

[イシュー #14257 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**以前の動作**

HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。 ただし、この影響を受けていたのはデータベースの作成だけです。 追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。 また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。

**新しい動作**

追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。 これは、SpatiaLite の AddGeometryColumn 関数の基になる動作と厳密に一致します。

**理由**

列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。

**軽減策**

`HasColumnType` を使用してディメンションを指定します。

```cs
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

必須の依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。

依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。

```cs
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

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

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

```cs
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

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

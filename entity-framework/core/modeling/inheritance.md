---
title: 継承-EF Core
description: Entity Framework Core を使用してエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 11bd653a53767aa732790b1222da1beff8ad26a9
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635758"
---
# <a name="inheritance"></a>継承

EF では、.NET 型階層をデータベースにマップできます。 これにより、基本型と派生型を使用して、通常どおりに .NET エンティティをコードに記述し、EF によって適切なデータベーススキーマの作成、クエリの実行などをシームレスに行うことができます。型階層のマッピング方法の実際の詳細は、プロバイダーに依存します。このページでは、リレーショナルデータベースのコンテキストでの継承のサポートについて説明します。

## <a name="entity-type-hierarchy-mapping"></a>エンティティ型階層のマッピング

慣例により、EF は基本型または派生型を自動的にスキャンしません。これは、階層内の CLR 型をマップする必要がある場合は、モデルでその型を明示的に指定する必要があることを意味します。 たとえば、階層の基本データ型だけを指定しても、EF Core によってそのすべてのサブ型が暗黙的に含まれるとは限りません。

次の例では、とそのサブクラスの DbSet を公開して `Blog` `RssBlog` います。 `Blog`に他のサブクラスがある場合は、モデルには含まれません。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。 たとえば、列は `RssUrl` null 値を許容 `Blog` します。通常のインスタンスにはそのプロパティがないためです。

階層内の1つ以上のエンティティに対してを公開しない場合は、 `DbSet` FLUENT API を使用してモデルに含まれていることを確認することもできます。

> [!TIP]
> 規則に依存しない場合は、を使用して基本型を明示的に指定でき `HasBaseType` ます。 また、を使用し `.HasBaseType((Type)null)` て、階層からエンティティ型を削除することもできます。

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>階層ごとのテーブルと識別子の構成

既定では、EF は、 *階層単位* (TPH) パターンを使用して継承をマップします。 TPH は、1つのテーブルを使用して階層内のすべての型のデータを格納します。また、識別子列を使用して、各行が表す型を識別します。

上記のモデルは、次のデータベーススキーマにマップされます (各行に格納されているの型を識別する、暗黙的に作成された列に注意して `Discriminator` `Blog` ください)。

![階層ごとのテーブルパターンを使用してブログエンティティ階層にクエリを実行した結果のスクリーンショット](_static/inheritance-tph-data.png)

識別子の列の名前と種類と、階層内の各型を識別するために使用される値を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

上の例では、EF によって、階層の基本エンティティに対して暗黙的に [シャドウプロパティ](xref:core/modeling/shadow-properties) として識別子が追加されています。 このプロパティは、次のように構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

また、識別子は、エンティティ内の通常の .NET プロパティにもマップできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

TPH パターンを使用する派生エンティティに対してクエリを実行すると、EF Core によってクエリの識別子列に述語が追加されます。 このフィルターにより、結果に含まれていない基本データ型または兄弟型に対して追加の行が取得されないようにします。 基本エンティティに対してクエリを実行すると、階層内のすべてのエンティティの結果が取得されるため、基本エンティティ型に対してこのフィルター述語はスキップされます。 クエリから結果を具体化する場合、モデル内のエンティティ型にマップされていない識別子の値を使用すると、結果を具体化する方法がわからないため、例外がスローされます。 このエラーが発生するのは、EF モデルでマップされていない識別子の値を持つ行がデータベースに含まれている場合のみです。 このようなデータがある場合は、EF Core モデルの識別子マッピングを不完全としてマークして、階層内の任意の型に対してクエリを実行するためのフィルター述語を常に追加する必要があることを示すことができます。 `IsComplete(false)` 識別子の構成でを呼び出すと、マッピングは不完全としてマークされます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a>共有列

既定では、階層内の2つの兄弟エンティティ型に同じ名前のプロパティがある場合、2つの異なる列にマップされます。 ただし、型が同一の場合は、同じデータベース列にマップできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>テーブルごとの構成

> [!NOTE]
> TPT (テーブル単位) 機能は EF Core 5.0 で導入されました。 EF6 では、具象型 (TPC) ごとにサポートされていますが、まだ EF Core サポートされていません。

TPT mapping パターンでは、すべての型が個々のテーブルにマップされます。 基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。 派生型にマップされるテーブルには、派生テーブルとベーステーブルを結合する外部キーも格納されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF は、上記のモデルに対して次のデータベーススキーマを作成します。

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> Primary key 制約の名前を変更すると、階層にマップされているすべてのテーブルに新しい名前が適用されます。将来の EF バージョンでは、 [問題 19970](https://github.com/dotnet/efcore/issues/19970) が修正されると、特定のテーブルに対してのみ制約の名前を変更できるようになります。

一括構成を使用している場合は、を呼び出して、特定のテーブルの列名を取得でき <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]

> [!WARNING]
> 多くの場合、TPT は TPH と比較すると、パフォーマンスが低下していることを示しています。 [詳細については、パフォーマンスに関するドキュメントを参照して](xref:core/performance/modeling-for-performance#inheritance-mapping)ください。

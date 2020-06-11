---
title: 継承-EF Core
description: Entity Framework Core を使用してエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664053"
---
# <a name="inheritance"></a>継承

EF では、.NET 型階層をデータベースにマップできます。 これにより、基本型と派生型を使用して、通常どおりに .NET エンティティをコードに記述し、EF によって適切なデータベーススキーマの作成、クエリの実行などをシームレスに行うことができます。型階層のマッピング方法の実際の詳細は、プロバイダーに依存します。このページでは、リレーショナルデータベースのコンテキストでの継承のサポートについて説明します。

現時点では、EF Core では、階層単位 (TPH) のパターンのみがサポートされています。 TPH は、1つのテーブルを使用して階層内のすべての型のデータを格納します。また、識別子列を使用して、各行が表す型を識別します。

> [!NOTE]
> EF6 によってサポートされている、型ごとのテーブル (TPT) と具象型 (TPC) は、EF Core ではまだサポートされていません。 TPT は EF Core 5.0 に対して計画されている主な機能です。

## <a name="entity-type-hierarchy-mapping"></a>エンティティ型階層のマッピング

慣例により、EF は基本型または派生型を自動的にスキャンしません。これは、階層内の CLR 型をマップする必要がある場合は、モデルでその型を明示的に指定する必要があることを意味します。 たとえば、階層の基本データ型だけを指定しても、EF Core によってそのすべてのサブ型が暗黙的に含まれるとは限りません。

次の例では、とそのサブクラスの DbSet を公開して `Blog` `RssBlog` います。 `Blog`に他のサブクラスがある場合は、モデルには含まれません。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

このモデルは、次のデータベーススキーマにマップされます (それぞれの行に格納されている*ブログ*の種類を識別する、暗黙的に作成された*識別子*の列に注意してください)。

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。 たとえば、 *RssUrl*列は null 値を許容します。通常の*ブログ*インスタンスにはそのプロパティがないためです。

階層内の1つ以上のエンティティに対して DbSet を公開しない場合は、Fluent API を使用してモデルに含まれていることを確認することもできます。

> [!TIP]
> 規則に依存しない場合は、を使用して基本型を明示的に指定でき `HasBaseType` ます。 また、を使用し `.HasBaseType((Type)null)` て、階層からエンティティ型を削除することもできます。

## <a name="discriminator-configuration"></a>識別子の構成

識別子の列の名前と種類と、階層内の各型を識別するために使用される値を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

上の例では、EF によって、階層の基本エンティティに対して暗黙的に[シャドウプロパティ](xref:core/modeling/shadow-properties)として識別子が追加されています。 このプロパティは、次のように構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

また、識別子は、エンティティ内の通常の .NET プロパティにもマップできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a>共有列

既定では、階層内の2つの兄弟エンティティ型に同じ名前のプロパティがある場合、2つの異なる列にマップされます。 ただし、型が同一の場合は、同じデータベース列にマップできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

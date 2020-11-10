---
title: バッキングフィールド-EF Core
description: Entity Framework Core モデルでのプロパティのバッキングフィールドの構成
author: ajcvickers
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 0df54192da7365451e5213739df8117e66c5cd0f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429664"
---
# <a name="backing-fields"></a>バッキング フィールド

バッキングフィールドを使用すると、EF はプロパティではなくフィールドに対して読み取りや書き込みを行うことができます。 これは、クラス内のカプセル化を使用して、アプリケーションコードによるデータへのアクセスを制限したり、データへのアクセスを制限したりする場合に便利ですが、このような制限や機能強化を使用せずに、データベースに対して値の読み取りや書き込みを行う必要があります。

## <a name="basic-configuration"></a>基本構成

慣例により、次のフィールドは、特定のプロパティのバッキングフィールドとして検出されます (優先順位順に一覧表示されます)。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

次の例では、 `Url` プロパティはバッキングフィールドとしてを持つように構成されてい `_url` ます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

バッキングフィールドは、モデルに含まれているプロパティに対してのみ検出されることに注意してください。 モデルに含まれるプロパティの詳細については、「 [プロパティを除外 & を含める](xref:core/modeling/entity-properties)」を参照してください。

データ注釈 (EFCore 5.0 で利用可能) または Fluent API を使用してバッキングフィールドを構成することもできます。たとえば、フィールド名が上記の規則に対応していない場合です。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

***

## <a name="field-and-property-access"></a>フィールドアクセスとプロパティアクセス

既定では、EF は、バッキングフィールドが適切に構成されていることを前提として常に読み取りと書き込みを行います。プロパティは使用されません。 ただし、EF では他のアクセスパターンもサポートされています。 たとえば、次の例では、を具体化するときにのみバッキングフィールドに書き込むように EF に指示し、その他のすべての場合にプロパティを使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

サポートされているオプションの完全なセットについては、「 [Propertyaccessmode 列挙型](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) 」を参照してください。

> [!NOTE]
> EF Core 3.0 では、既定のプロパティアクセスモードはからに変更されました `PreferFieldDuringConstruction` `PreferField` 。

## <a name="field-only-properties"></a>フィールドのみのプロパティ

また、エンティティクラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成することもできますが、代わりにフィールドを使用してエンティティにデータを格納します。 これは、データがエンティティの CLR 型ではなく、変更トラッカーに格納される [シャドウプロパティ](xref:core/modeling/shadow-properties)とは異なります。 フィールドのみのプロパティは、一般に、エンティティクラスがプロパティの代わりにメソッドを使用して値を取得または設定する場合、またはフィールドがドメインモデル内でまったく公開されない場合 (主キーなど) に使用されます。

API に名前を指定することで、フィールドのみのプロパティを構成でき `Property(...)` ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF は、指定された名前を持つ CLR プロパティを見つけようとします。プロパティが見つからない場合は、フィールドを検索します。 プロパティもフィールドも見つからない場合は、代わりに shadow プロパティが設定されます。

LINQ クエリからはフィールドのみのプロパティを参照する必要がありますが、このようなフィールドは通常はプライベートです。 `EF.Property(...)`LINQ クエリでメソッドを使用して、フィールドを参照できます。

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

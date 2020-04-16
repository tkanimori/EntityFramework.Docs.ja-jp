---
title: バッキング フィールド - EF コア
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434176"
---
# <a name="backing-fields"></a>バッキング フィールド

バッキング フィールドを使用すると、EF はプロパティではなくフィールドの読み取りおよび書き込みを行えます。 これは、クラス内のカプセル化を使用して、アプリケーション コードによるデータへのアクセスに関するセマンティクスの使用を制限したり、そのいずれかを強化したりするときに役立ちますが、これらの制限や拡張を使用せずに値をデータベースから読み取ったり、データベースに書き込んだりする必要があります。

## <a name="basic-configuration"></a>基本構成

規則により、次のフィールドは、特定のプロパティのバッキング フィールドとして検出されます (優先順位に示されています)。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

次の例では、プロパティ`Url`がバッキング フィールド`_url`として設定されています。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

バッキング フィールドは、モデルに含まれるプロパティに対してのみ検出されることに注意してください。 モデルに含まれるプロパティの詳細については、「プロパティを[除外する&を含める](included-properties.md)」を参照してください。

バッキング フィールドは、データ注釈や Fluent API を使用して構成することもできます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>フィールドとプロパティへのアクセス

既定では、EF は常にバッキング フィールドに対して読み取りと書き込みを行います ( 適切に構成されていると仮定 ) 、 プロパティを使用することはありません。 ただし、EF は他のアクセス パターンもサポートしています。 たとえば、次のサンプルは、具体化中にのみバッキング フィールドに書き込み、その他すべての場合にプロパティを使用するように EF に指示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

サポートされているオプションの完全なセットについては、[プロパティアクセス モード列挙型](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)を参照してください。

> [!NOTE]
> EF Core 3.0 では、既定のプロパティ`PreferFieldDuringConstruction`アクセス`PreferField`モードがから に変更されました。

## <a name="field-only-properties"></a>フィールドのみのプロパティ

エンティティ クラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成し、代わりにフィールドを使用してエンティティにデータを格納することもできます。 これは、エンティティの CLR 型ではなく、変更トラッカーにデータが格納される[シャドウ プロパティ](shadow-properties.md)とは異なります。 フィールドのみのプロパティは、エンティティ クラスがプロパティではなくメソッドを使用して値を取得または設定する場合、またはドメイン モデルでフィールドをまったく公開しない場合 (主キーなど) で一般的に使用されます。

API で名前を指定することで、フィールドのみのプロパティを`Property(...)`構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF は、指定された名前の CLR プロパティ、またはプロパティが見つからない場合はフィールドを検索しようとします。 プロパティもフィールドも見つからない場合は、シャドウ プロパティが設定されます。

LINQ クエリからフィールドのみのプロパティを参照する必要がある場合がありますが、このようなフィールドは通常プライベートです。 このメソッドを`EF.Property(...)`LINQ クエリで使用して、フィールドを参照できます。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

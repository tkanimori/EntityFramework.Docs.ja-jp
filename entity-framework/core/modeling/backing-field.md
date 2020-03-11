---
title: バッキングフィールド-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 20cf9dc9b0d556f29680bce588bcbdc4ea48fa74
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414639"
---
# <a name="backing-fields"></a>バッキング フィールド

バッキングフィールドを使用すると、EF はプロパティではなくフィールドに対して読み取りや書き込みを行うことができます。 これは、クラス内のカプセル化を使用して、アプリケーションコードによるデータへのアクセスを制限したり、データへのアクセスを制限したりする場合に便利ですが、このような制限や機能強化を使用せずに、データベースに対して値の読み取りや書き込みを行う必要があります。

## <a name="basic-configuration"></a>基本構成

慣例により、次のフィールドは、特定のプロパティのバッキングフィールドとして検出されます (優先順位順に一覧表示されます)。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

次の例では、`Url` プロパティは、バッキングフィールドとして `_url` を持つように構成されています。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

バッキングフィールドは、モデルに含まれているプロパティに対してのみ検出されることに注意してください。 モデルに含まれるプロパティの詳細については、「[プロパティを除外 & を含める](included-properties.md)」を参照してください。

フィールド名が上記の規則に対応していない場合などに、バッキングフィールドを明示的に構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>フィールドアクセスとプロパティアクセス

既定では、EF は、バッキングフィールドが適切に構成されていることを前提として常に読み取りと書き込みを行います。プロパティは使用されません。 ただし、EF では他のアクセスパターンもサポートされています。 たとえば、次の例では、を具体化するときにのみバッキングフィールドに書き込むように EF に指示し、その他のすべての場合にプロパティを使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

サポートされているオプションの完全なセットについては、「 [Propertyaccessmode 列挙型](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)」を参照してください。

> [!NOTE]
> EF Core 3.0 では、既定のプロパティアクセスモードが `PreferFieldDuringConstruction` から `PreferField`に変更されました。

## <a name="field-only-properties"></a>フィールドのみのプロパティ

また、エンティティクラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成することもできますが、代わりにフィールドを使用してエンティティにデータを格納します。 これは、データがエンティティの CLR 型ではなく、変更トラッカーに格納される[シャドウプロパティ](shadow-properties.md)とは異なります。 フィールドのみのプロパティは、一般に、エンティティクラスがプロパティの代わりにメソッドを使用して値を取得または設定する場合、またはフィールドがドメインモデル内でまったく公開されない場合 (主キーなど) に使用されます。

フィールドのみのプロパティを構成するには、`Property(...)` API で名前を指定します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF は、指定された名前を持つ CLR プロパティを見つけようとします。プロパティが見つからない場合は、フィールドを検索します。 プロパティもフィールドも見つからない場合は、代わりに shadow プロパティが設定されます。

LINQ クエリからはフィールドのみのプロパティを参照する必要がありますが、このようなフィールドは通常はプライベートです。 LINQ クエリで `EF.Property(...)` メソッドを使用して、フィールドを参照できます。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

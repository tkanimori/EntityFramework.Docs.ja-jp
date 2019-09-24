---
title: バッキングフィールド-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: c3ca8bb97992c192672e8c2f2040b0de029df68d
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197484"
---
# <a name="backing-fields"></a>バッキング フィールド

> [!NOTE]  
> この機能は EF Core 1.1 で新たに追加されています。

バッキングフィールドを使用すると、EF はプロパティではなくフィールドに対して読み取りや書き込みを行うことができます。 これは、クラスのカプセル化を使用して、アプリケーションコードによるデータへのアクセスを制限したり、データへのアクセスを制限したりする場合に便利ですが、値は、これらの制限を使用せずに、データベースに対して読み取りや書き込みを行う必要があります。増強.

## <a name="conventions"></a>規約

慣例により、次のフィールドは、特定のプロパティのバッキングフィールドとして検出されます (優先順位順に一覧表示されます)。 フィールドは、モデルに含まれているプロパティに対してのみ検出されます。 モデルに含まれるプロパティの詳細については、「[プロパティを除外 & を含める](included-properties.md)」を参照してください。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

バッキングフィールドが構成されている場合、EF は、プロパティ setter を使用するのではなく、データベースからエンティティインスタンスを具体化するときに、そのフィールドに直接書き込みます。 EF が他のタイミングで値の読み取りまたは書き込みを行う必要がある場合は、可能であればプロパティを使用します。 たとえば、EF がプロパティの値を更新する必要がある場合、プロパティ set アクセス操作子が定義されていれば、そのプロパティを使用します。 プロパティが読み取り専用の場合は、フィールドに書き込みます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してバッキングフィールドを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティのバッキングフィールドを構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>フィールドが使用されるタイミングの制御

EF がフィールドまたはプロパティを使用するタイミングを構成できます。 サポートされているオプションについては、「 [Propertyaccessmode 列挙型](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)」を参照してください。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>プロパティのないフィールド

また、エンティティクラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成することもできますが、代わりにフィールドを使用してエンティティにデータを格納します。 これは、データが変更トラッカーに格納される[シャドウプロパティ](shadow-properties.md)とは異なります。 これは通常、エンティティクラスがメソッドを使用して値を取得/設定する場合に使用されます。

EF には、 `Property(...)` API 内のフィールドの名前を指定できます。 指定された名前のプロパティが存在しない場合、EF はフィールドを検索します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

フィールド名以外の名前をプロパティに指定することもできます。 この名前は、モデルを作成するときに使用されます。特に、データベース内のにマップされている列名に使用されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldConceptualProperty.cs#Sample)]

エンティティクラスにプロパティがない場合は、LINQ クエリで`EF.Property(...)`メソッドを使用して、モデルの概念の一部であるプロパティを参照できます。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

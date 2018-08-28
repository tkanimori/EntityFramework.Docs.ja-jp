---
title: バッキング フィールド - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 79221b6f7968675ff10f80d5df181b674b6a20c9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994094"
---
# <a name="backing-fields"></a>バッキング フィールド

> [!NOTE]  
> この機能は、EF Core 1.1 の新機能です。

バッキング フィールドは、読み取りまたは書き込みのプロパティではなく、フィールドに EF を使用できます。 これは、便利な場合、クラスにカプセル化は、アプリケーション コードでの使用を制限したり、データへのアクセスのセマンティクスの向上に使用されているが、値を読み取ったりやこれらの制限事項を使用せずに、データベースに書き込まれる必要があります/機能強化。

## <a name="conventions"></a>規約

慣例により、バッキング フィールド (優先順位の順序で表示されている) 特定のプロパティとして、次のフィールドが検出されます。 フィールドは、モデルに含まれているプロパティにのみ検出されます。 プロパティがモデルに含まれる詳細については、次を参照してください。[含める/除外するプロパティ](included-properties.md)します。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

バッキング フィールドが構成されている場合、EF は、(プロパティの set アクセス操作子を使用するのではなく) データベースからエンティティのインスタンスを具体化する場合にそのフィールドに直接書き込まれます。 EF の読み取りまたはそれ以外の時間の値を上書きする場合、可能であれば、プロパティが使用されます。 たとえば、EF では、プロパティの値を更新する必要がある場合が定義されている場合、プロパティ set アクセス操作子を使用、されます。 プロパティが読み取り専用の場合は、フィールドに書き込まれます。

## <a name="data-annotations"></a>データの注釈

バッキング フィールドは、データ注釈で構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティのバッキング フィールドを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>フィールドを使用する場合を制御します。

EF は、フィールドまたはプロパティを使用する場合を構成することができます。 参照してください、 [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)のサポートされるオプション。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>プロパティ フィールド

エンティティ クラスに対応する CLR プロパティはありませんが、代わりに、エンティティにデータを格納するフィールドを使用するモデルの概念のプロパティを作成することもできます。 これは、異なる[シャドウ プロパティ](shadow-properties.md)、変更トラッカーにデータを格納する場所。 これは通常、使用、エンティティ クラスでは、メソッドを使用して、値を取得または設定する場合。

EF のフィールドの名前を指定できます、 `Property(...)` API。 指定した名前のプロパティがない場合は、EF はフィールドになります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

プロパティ、フィールド名以外の名前を選択することもできます。 この名前は、モデルを作成するときに使用し、データベース内にマップされている列名を使用する最も顕著な。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

エンティティ クラスのプロパティがない、ときに使用できます、`EF.Property(...)`メソッドで LINQ クエリを概念的には、モデルの一部であるプロパティを参照してください。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

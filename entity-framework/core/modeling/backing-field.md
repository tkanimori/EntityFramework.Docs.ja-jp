---
title: "EF コアをバックアップするには、フィールド-"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
ms.technology: entity-framework-core
uid: core/modeling/backing-field
ms.openlocfilehash: e95417b3368d09a64f9ec02ae40c38dc770c2e6b
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="backing-fields"></a>バッキング フィールド

> [!NOTE]  
> この機能は、EF コア 1.1 の新機能です。

バッキング フィールドには、読み取りまたは書き込みのプロパティではなく、フィールドを行う EF ができるようにします。 これを利用するクラスにカプセル化は、アプリケーション コードでの使用を制限したり、データへのアクセスの周囲のセマンティクスを強化する使用されているが、値の読み取りまたはその制限を使用せずに、データベースに書き込まれる必要があります/拡張機能です。

## <a name="conventions"></a>規則

慣例により、バックアップする (優先順位順に表示されます)、指定したプロパティ フィールドとして、次のフィールドが検出されます。 フィールドは、モデルに含まれているプロパティに対してのみ検出されます。 詳細については、モデルに含まれるプロパティを次を参照してください。[などとプロパティを除外](included-properties.md)です。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

バッキング フィールドが構成されている場合、EF は、(プロパティ set アクセス操作子を使用するのではなく) データベースからのエンティティのインスタンスを具体化する場合にそのフィールドに直接書き込まれます。 EF を他のタイミングで値を読み取ったり書き込んだりする場合は、可能であれば、プロパティが使用されます。 たとえば、EF プロパティの値を更新する場合は、使用されますプロパティ set アクセス操作子が定義されている場合。 プロパティが読み取り専用である場合は、フィールドに書き込まれます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用したフィールドをバックアップを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティのバッキング フィールドを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>フィールドを使用する場合を制御します。

EF フィールドまたはプロパティを使用する場合を構成することができます。 参照してください、 [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)用にサポートされるオプションです。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>プロパティ フィールド

エンティティ クラスに対応する CLR プロパティはありませんが、代わりに、エンティティにデータを格納するフィールドを使用するモデルの概念プロパティを作成することもできます。 これとは異なる[プロパティをシャドウ](shadow-properties.md)、変更トラッカーのデータを格納する場所です。 これは通常される使用、そのエンティティ クラスでは、メソッドを使用して、値を取得または設定する場合。

EF 内のフィールドの名前を指定する、 `Property(...)` API です。 場合は、指定した名前のプロパティがない、EF はフィールドのなります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

プロパティ、フィールド名以外の名前を入力することもできます。 この名前は、モデルを作成するときに使用し、データベース内にマップされている列の名前を使用する最も顕著なです。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

エンティティ クラスでプロパティがない場合は、使用できます、`EF.Property(...)`概念的には、モデルの一部であるプロパティを参照する LINQ クエリ内のメソッドです。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

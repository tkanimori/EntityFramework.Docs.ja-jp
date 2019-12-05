---
title: キー (プライマリ)-EF Core
description: Entity Framework Core を使用する場合のエンティティ型のキーの構成方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824615"
---
# <a name="keys-primary"></a>キー (主)

キーは、各エンティティインスタンスのプライマリ一意識別子として機能します。 リレーショナルデータベースを使用する場合、これは*主キー*の概念にマップされます。 また、主キーではない一意の識別子を構成することもできます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。

次のいずれかの方法を使用して、主キーの設定/作成を行うことができます。

## <a name="conventions"></a>規約

既定では、`Id` または `<type name>Id` という名前のプロパティは、エンティティのキーとして構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> 所有されている[エンティティ型](xref:core/modeling/owned-entities)は、キーを定義するために異なる規則を使用します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、1つのプロパティをエンティティのキーとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、1つのプロパティをエンティティのキーとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

また、Fluent API を使用して、エンティティ (複合キーと呼ばれます) のキーとして複数のプロパティを構成することもできます。 複合キーは、Fluent API を使用してのみ構成できます。複合キーは設定されず、データ注釈を使用して構成することはできません。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a>キーの種類と値

EF Core では、`string`、`Guid`、`byte[]` など、任意のプリミティブ型のプロパティを主キーとして使用できます。 ただし、すべてのデータベースでサポートされているわけではありません。 場合によっては、キー値がサポートされている型に自動的に変換されることがあります。それ以外の場合は、[手動で](xref:core/modeling/value-conversions)変換を指定する必要があります。

新しいエンティティをコンテキストに追加する場合、キープロパティには既定値以外の値が常に設定されている必要がありますが、[データベースによって生成](xref:core/modeling/generated-properties)される型もあります。 その場合、EF は、追跡のためにエンティティが追加されると、一時値の生成を試みます。 [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)が呼び出された後、一時的な値はデータベースによって生成される値に置き換えられます。

> [!Important]
> キープロパティにデータベースによって生成された値があり、エンティティの追加時に既定以外の値が指定されている場合、EF はそのエンティティがデータベースに既に存在していると見なし、新しいエンティティを挿入する代わりに更新しようとします。 これを回避するには、値の生成をオフにするか、[生成されたプロパティに明示的な値を指定する方法](../saving/explicit-values-generated-properties.md)を参照してください。
---
title: キー (プライマリ)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 66c64c389294e8e109a614a2bea8311932660dea
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655943"
---
# <a name="keys-primary"></a>キー (主)

キーは、各エンティティインスタンスのプライマリ一意識別子として機能します。 リレーショナルデータベースを使用する場合、これは*主キー*の概念にマップされます。 また、主キーではない一意の識別子を構成することもできます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。

次のいずれかの方法を使用して、主キーの設定/作成を行うことができます。

## <a name="conventions"></a>規約

慣例により、`Id` または `<type name>Id` という名前のプロパティは、エンティティのキーとして構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyIdhighlight=3)]

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、1つのプロパティをエンティティのキーとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、1つのプロパティをエンティティのキーとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

また、Fluent API を使用して、エンティティ (複合キーと呼ばれます) のキーとして複数のプロパティを構成することもできます。 複合キーは、Fluent API を使用してのみ構成できます。複合キーは設定されず、データ注釈を使用して構成することはできません。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

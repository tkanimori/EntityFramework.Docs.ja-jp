---
title: キー (プライマリ) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 51d163b867085f42f415dbd7afa9e311ab1781a0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929837"
---
# <a name="keys-primary"></a>キー (プライマリ)

キーは、各エンティティ インスタンスのプライマリの一意の識別子として機能します。 リレーショナル データベースを使用する場合はこれはマップの概念、*主キー*します。 主キーでない一意の識別子を構成することもできます (を参照してください[代替キー](alternate-keys.md)詳細については)。 

次のいずれかのセットアップ/主キーの作成に使用できます。

## <a name="conventions"></a>規約

プロパティの名前付け規則、により`Id`または`<type name>Id`はエンティティのキーとして構成されます。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=11,12)]

キー (複合キーと呼ばれる) エンティティの複数のプロパティを構成するのに Fluent API を使用することもできます。 Fluent API を使用して複合キーを構成することができますのみ - 規則は複合キーをセットアップしないと、いずれかを構成するデータの注釈は使用できません。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=11,12)]

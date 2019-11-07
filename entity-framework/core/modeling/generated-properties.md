---
title: 生成された値-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
uid: core/modeling/generated-properties
ms.openlocfilehash: 6643d3c5c9b3363e450e820793f449a41e2eba80
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655747"
---
# <a name="generated-values"></a>生成された値

## <a name="value-generation-patterns"></a>値生成パターン

プロパティに使用できる値生成パターンには、次の3つがあります。

* 値の生成なし
* 追加時に生成される値
* 追加または更新時に生成される値

### <a name="no-value-generation"></a>値の生成なし

値の生成は、常にデータベースに保存される有効な値を指定することを意味します。 この有効な値は、コンテキストに追加する前に、新しいエンティティに割り当てる必要があります。

### <a name="value-generated-on-add"></a>追加時に生成される値

追加時に生成される値は、新しいエンティティに対して値が生成されることを意味します。

使用するデータベースプロバイダーによっては、値が EF またはデータベースでクライアント側で生成される場合があります。 値がデータベースによって生成される場合、エンティティをコンテキストに追加するときに、EF によって一時的な値が割り当てられることがあります。 この一時的な値は、`SaveChanges()`時にデータベースによって生成された値に置き換えられます。

プロパティに値が割り当てられているコンテキストにエンティティを追加すると、EF は新しい値を生成するのではなく、その値を挿入しようとします。 CLR の既定値が割り当てられていない場合、プロパティに値が割り当てられていると見なされます (`string`の場合は`null`、`int`の場合は `0`、`Guid.Empty` の場合は `Guid`など)。 詳細については、「[生成されるプロパティの明示的な値](../saving/explicit-values-generated-properties.md)」を参照してください。

> [!WARNING]  
> 追加されたエンティティに対して値が生成される方法は、使用されているデータベースプロバイダーによって異なります。 データベースプロバイダーでは、一部のプロパティの種類に対して値の生成が自動的に設定される場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。
>
> たとえば、SQL Server を使用すると、`GUID` のプロパティ (SQL Server シーケンシャル GUID アルゴリズムを使用) に対して値が自動的に生成されます。 ただし、`DateTime` プロパティが追加時に生成されるように指定した場合は、値を生成する方法を設定する必要があります。 これを行う1つの方法として、`GETDATE()`の既定値を構成する方法があります。[既定値](relational/default-values.md)を参照してください。

### <a name="value-generated-on-add-or-update"></a>追加または更新時に生成される値

追加または更新時に生成される値は、レコードが保存されるたびに新しい値が生成されることを意味します (insert または update)。

`value generated on add`と同様に、新しく追加されたエンティティのインスタンスのプロパティの値を指定すると、生成される値ではなく、その値が挿入されます。 更新時に明示的な値を設定することもできます。 詳細については、「[生成されるプロパティの明示的な値](../saving/explicit-values-generated-properties.md)」を参照してください。

> [!WARNING]
> 追加および更新されたエンティティの値がどのように生成されるかは、使用されているデータベースプロバイダーによって異なります。 データベースプロバイダーは、プロパティの種類によっては値の生成を自動的に設定する場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。
>
> たとえば、`byte[]` SQL Server を使用する場合、追加または更新時に生成されるように設定され、同時実行トークンとしてマークされるプロパティは、データベースに値が生成されるように、`rowversion` データ型で設定されます。 ただし、`DateTime` プロパティが追加または更新時に生成されるように指定した場合は、値を生成する方法を設定する必要があります。 これを行う1つの方法として、`GETDATE()` の既定値 ([既定値](relational/default-values.md)を参照) を構成して新しい行の値を生成する方法があります。 次に、データベーストリガーを使用して、更新中に値を生成することができます (次の例のトリガーなど)。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>規約

慣例により、short、int、long、または Guid 型の非複合主キーは、追加時に値を生成するように設定されます。 その他のすべてのプロパティは、値の生成なしで設定されます。

## <a name="data-annotations"></a>データの注釈

### <a name="no-value-generation-data-annotations"></a>値の生成なし (データ注釈)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>加算時に生成される値 (データ注釈)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> これにより、追加されたエンティティに対して値が生成されたことを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することは保証されません。 詳細については、「追加」セクション[で生成された値](#value-generated-on-add)を参照してください。

### <a name="value-generated-on-add-or-update-data-annotations"></a>追加または更新 (データ注釈) に対して生成される値

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、追加または更新されたエンティティに対して値が生成されたことを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することは保証されません。 詳細について[は、「追加または更新セクションで生成された値](#value-generated-on-add-or-update)」を参照してください。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、特定のプロパティの値生成パターンを変更できます。

### <a name="no-value-generation-fluent-api"></a>値の生成なし (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>追加時に生成された値 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` は、追加されたエンティティに対して値が生成されることを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することを保証しません。  詳細については、「追加」セクション[で生成された値](#value-generated-on-add)を参照してください。

### <a name="value-generated-on-add-or-update-fluent-api"></a>追加または更新 (Fluent API) で生成された値

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、追加または更新されたエンティティに対して値が生成されたことを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することは保証されません。 詳細について[は、「追加または更新セクションで生成された値](#value-generated-on-add-or-update)」を参照してください。

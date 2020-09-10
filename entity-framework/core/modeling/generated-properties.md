---
title: 生成された値-EF Core
description: Entity Framework Core を使用する場合のプロパティの値生成の構成方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: a10ed3522c255f18d31a75e237a5507fdba468a1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619367"
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

使用するデータベースプロバイダーによっては、値が EF またはデータベースでクライアント側で生成される場合があります。 値がデータベースによって生成される場合、エンティティをコンテキストに追加するときに、EF によって一時的な値が割り当てられることがあります。 この一時値は、の間にデータベースによって生成された値に置き換えられ `SaveChanges()` ます。

プロパティに値が割り当てられているコンテキストにエンティティを追加すると、EF は新しい値を生成するのではなく、その値を挿入しようとします。 CLR の既定値 (for、for、for など) が割り当てられていない場合、プロパティには値が割り当てられていると見なされ `null` `string` `0` `int` `Guid.Empty` `Guid` ます。 詳細については、「 [生成されるプロパティの明示的な値](xref:core/saving/explicit-values-generated-properties)」を参照してください。

> [!WARNING]
> 追加されたエンティティに対して値が生成される方法は、使用されているデータベースプロバイダーによって異なります。 データベースプロバイダーでは、一部のプロパティの種類に対して値の生成が自動的に設定される場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。
>
> たとえば、SQL Server を使用すると、プロパティに対して値が自動的に生成され `GUID` ます (SQL Server シーケンシャル GUID アルゴリズムを使用)。 ただし、プロパティが追加時に生成されるように指定する場合は、 `DateTime` 値を生成する方法を設定する必要があります。 これを行う1つの方法は、既定値のを構成することです `GETDATE()` 。「 [既定値](#default-values)」を参照してください。

### <a name="value-generated-on-add-or-update"></a>追加または更新時に生成される値

追加または更新時に生成される値は、レコードが保存されるたびに新しい値が生成されることを意味します (insert または update)。

と同様に `value generated on add` 、エンティティの新しく追加されたインスタンスでプロパティの値を指定すると、生成される値ではなく、その値が挿入されます。 更新時に明示的な値を設定することもできます。 詳細については、「 [生成されるプロパティの明示的な値](xref:core/saving/explicit-values-generated-properties)」を参照してください。

> [!WARNING]
> 追加および更新されたエンティティの値がどのように生成されるかは、使用されているデータベースプロバイダーによって異なります。 データベースプロバイダーは、プロパティの種類によっては値の生成を自動的に設定する場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。
>
> たとえば、SQL Server を使用する場合、 `byte[]` 追加または更新時に生成されるように設定され、同時実行トークンとしてマークされているプロパティは、データ型を使用して設定される `rowversion` ため、データベースに値が生成されます。 ただし、 `DateTime` プロパティが追加または更新時に生成されるように指定する場合は、値を生成する方法を設定する必要があります。 これを行う1つの方法は、の既定値 `GETDATE()` ( [既定値](#default-values)を参照) を構成して、新しい行の値を生成することです。 次に、データベーストリガーを使用して、更新中に値を生成することができます (次の例のトリガーなど)。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>追加時に生成される値

慣例として、short、int、long、または Guid 型の非複合主キーは、値がアプリケーションによって提供されていない場合、挿入されたエンティティに対して値が生成されるように設定されます。 通常、データベースプロバイダーは必要な構成を行います。たとえば、SQL Server の数値主キーは、ID 列として自動的に設定されます。

挿入されたエンティティに対して値が生成されるようにプロパティを構成するには、次のようにします。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> これにより、追加されたエンティティに対して値が生成されたことを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することは保証されません。 詳細については、「追加」セクション [で生成された値](#value-generated-on-add) を参照してください。

### <a name="default-values"></a>既定の値

リレーショナルデータベースでは、既定値を使用して列を構成できます。その列の値を指定せずに行が挿入されると、既定値が使用されます。

プロパティの既定値を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

既定値の計算に使用される SQL フラグメントを指定することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

既定値を指定すると、プロパティは、追加時に生成される値として暗黙的に構成されます。

## <a name="value-generated-on-add-or-update"></a>追加または更新時に生成される値

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> これにより、追加または更新されたエンティティに対して値が生成されたことを EF が認識できるだけでなく、EF が実際のメカニズムを設定して値を生成することは保証されません。 詳細について [は、「追加または更新セクションで生成された値](#value-generated-on-add-or-update) 」を参照してください。

### <a name="computed-columns"></a>計算列

一部のリレーショナルデータベースでは、データベース内の値を計算するように列を構成できます。通常は、他の列を参照する式を使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=ComputedColumn&highlight=5)]

> [!NOTE]
> 場合によっては、列の値がフェッチされるたび ( *仮想* 列とも呼ばれます) に計算され、それ以外の場合は、行のすべての更新で計算され、格納されます ( *保存さ* れた列 *または保存* された列とも呼ばれます)。 これは、データベースプロバイダーによって異なります。

## <a name="no-value-generation"></a>値の生成なし

プロパティの値生成を無効にすることは、通常、規則によって値の生成用に構成される場合に必要です。 たとえば、int 型の主キーがある場合、それは暗黙的に add によって生成される値として設定されます。これは、次の方法で無効にできます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***

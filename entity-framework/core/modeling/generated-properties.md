---
title: 生成された値 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
uid: core/modeling/generated-properties
ms.openlocfilehash: 9ecfa924a0614f327f0bd202cb7dda95bea810af
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250700"
---
# <a name="generated-values"></a>生成された値

## <a name="value-generation-patterns"></a>値の生成のパターン

プロパティを使用できる 3 つの値の生成パターンがないです。
* 値を生成しません。
* 生成された値を追加します。
* 生成された値で追加または更新

### <a name="no-value-generation"></a>値を生成しません。

値の生成がありません、データベースに保存する有効な値を常に指定されます。 この有効な値は、コンテキストに追加される前に新しいエンティティに割り当てる必要があります。

### <a name="value-generated-on-add"></a>生成された値を追加します。

生成された値が意味を追加する新しいエンティティの値が生成されます。

使用されているデータベース プロバイダーによって、値があります EF によって、またはデータベース内のクライアント側を生成します。 値は、データベースによって生成、し、EF 割り当てられる一時的な値をコンテキストにエンティティを追加するとします。 この一時的な値は中にデータベースで生成された値で置き換えられます`SaveChanges()`します。

プロパティに割り当てられている値を持つコンテキストにエンティティを追加する場合、EF を新たに生成するのではなく、その値を挿入しようとします。 プロパティは、CLR の既定値が割り当てられていない場合に割り当てられている値を持つと見なされます (`null`の`string`、`0`の`int`、`Guid.Empty`の`Guid`など。)。 詳細については、[生成されたプロパティの値を明示的](../saving/explicit-values-generated-properties.md)を参照してください。

> [!WARNING]  
> 追加されたエンティティの値を生成する方法は、使用中のデータベース プロバイダーによって異なります。 データベース プロバイダーがいくつかのプロパティの型の値を生成をセットアップして自動的には、値を生成する方法を手動で設定する他のユーザー必要があります。
>
> たとえば、SQL Server を使用する場合の値が自動的に生成されますの`GUID`プロパティ (SQL Server のシーケンシャルな GUID アルゴリズムを使用して)。 ただし、ことを指定する場合、`DateTime`プロパティが生成されるで追加の値を生成する方法をセットアップする必要があります。 既定値を構成するには、1 つの方法は、`GETDATE()`を参照してください[既定値](relational/default-values.md)します。

### <a name="value-generated-on-add-or-update"></a>生成された値で追加または更新

生成された値で追加または更新は、レコードが (挿入または更新) を保存するたびに、新しい値が生成されることを意味します。

ような`value generated on add`新しく追加された値が生成される値ではなく挿入することは、エンティティのインスタンスでプロパティの値を指定する場合、します。 更新するときに、明示的な値を設定することもできます。 詳細については、[生成されたプロパティの値を明示的](../saving/explicit-values-generated-properties.md)を参照してください。

> [!WARNING]
> 追加および更新されたエンティティの値を生成する方法は、使用中のデータベース プロバイダーによって異なります。 データベース プロバイダー、他のユーザーが必要とする値を生成する方法を手動で設定するプロパティの種類によっての値を生成が自動的にセットアップ可能性があります。
> 
> たとえば、SQL Server を使用して`byte[]`で生成されるように設定されているプロパティの追加または更新、および同時実行トークンとしてマークされているに設定されます。、`rowversion`データ型の値は、データベースで生成するようにします。 ただし、ことを指定する場合、`DateTime`プロパティが生成されるで追加または更新の値を生成する方法をセットアップする必要があります。 既定値を構成するには、1 つの方法は、 `GETDATE()` (を参照してください[既定値](relational/default-values.md)) を新しい行の値を生成します。 データベース トリガーを使用して、(次の例のトリガー) などの更新中に値を生成する可能性があります。
> 
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>規約

慣例により、型 short、int 型の非複合主キーが長い、または Guid をセットアップして、追加で生成された値となります。 その他のすべてのプロパティ値を生成しないとセットアップになります。

## <a name="data-annotations"></a>データの注釈

### <a name="no-value-generation-data-annotations"></a>値を生成しない (データ注釈)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>生成された値 (データ注釈) を追加します。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> これにより、EF に追加されたエンティティの値が生成される、EF が実際の値を生成するメカニズムを設定することが保証されないことを通知ことだけです。 参照してください[で生成された値を追加](#value-generated-on-add)詳細セクション。

### <a name="value-generated-on-add-or-update-data-annotations"></a>生成された値を追加または更新 (データ注釈)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、EF に通知を追加または更新されたエンティティの値が生成される、EF が実際の値を生成するメカニズムを設定することは保証されないことだけです。 参照してください[で生成された値を追加または更新](#value-generated-on-add-or-update)詳細セクション。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、特定のプロパティの値の生成のパターンを変更することができます。

### <a name="no-value-generation-fluent-api"></a>値を生成しない (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>生成された値 (Fluent API) を追加します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` だけが EF に追加されたエンティティの値が生成される、EF が実際の値を生成するメカニズムを設定することが保証されないことを通知することができます。  参照してください[で生成された値を追加](#value-generated-on-add)詳細セクション。

### <a name="value-generated-on-add-or-update-fluent-api"></a>生成された値を追加または更新 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、EF に通知を追加または更新されたエンティティの値が生成される、EF が実際の値を生成するメカニズムを設定することは保証されないことだけです。 参照してください[で生成された値を追加または更新](#value-generated-on-add-or-update)詳細セクション。

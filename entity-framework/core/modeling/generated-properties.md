---
title: "生成された値: EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
ms.technology: entity-framework-core
uid: core/modeling/generated-properties
ms.openlocfilehash: 2d79bf1339ebe522c39fe8971d908c30e1f4dca0
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="generated-values"></a>生成される値

## <a name="value-generation-patterns"></a>値の生成のパターン

プロパティを使用できる 3 つの値生成パターンがあります。

### <a name="no-value-generation"></a>値を生成しません。

値を生成ないことを意味をデータベースに保存する有効な値を常に指定されます。 この有効な値は、コンテキストに追加される前に新しいエンティティに割り当てる必要があります。

### <a name="value-generated-on-add"></a>生成された値を追加します。

生成された値の追加手段値は、新しいエンティティを生成します。

使用されているデータベース プロバイダーによって、値があります EF またはデータベース内のクライアント側を生成します。 値は、データベースによって生成される場合、し、EF 可能性があります値を割り当てる一時コンテキストにエンティティを追加するとします。 この一時的な値は、中にデータベースで生成された値で置き換えられます`SaveChanges()`です。

プロパティに割り当てられている値を持つコンテキストにエンティティを追加する場合、EF を新たに生成するのではなく、その値を挿入しようとします。 プロパティは、CLR の既定値が割り当てられていない場合に割り当てられた値を持つと見なされます (`null`の`string`、`0`の`int`、`Guid.Empty`の`Guid`, などです。)。 詳細については、次を参照してください。[生成されたプロパティの値を明示的](..\saving\explicit-values-generated-properties.md)です。

> [!WARNING]  
> 追加されたエンティティの値を生成する方法は、使用中のデータベース プロバイダーによって異なります。 データベース プロバイダーのプロパティの種類によっての値を生成可能性があります自動セットアップしますが、他のユーザーがあります値を生成する方法を手動で設定します。
>
> たとえば、SQL Server を使用する場合の値に自動的にに対して生成されます`GUID`プロパティ (アルゴリズムを使用して、SQL Server シーケンシャル GUID)。 ただし、設定した場合、`DateTime`プロパティは、生成に追加する値を生成するための手段をセットアップする必要があります。 既定値を構成するには、1 つの方法は、`GETDATE()`を参照してください[既定値](relational/default-values.md)です。

### <a name="value-generated-on-add-or-update"></a>生成された値で追加または更新

生成された値に追加または更新プログラムが (insert または update) レコードを保存するたびに、新しい値が生成されることを意味します。

同様に`value generated on add`新しく追加された値が生成される値ではなく挿入することは、エンティティのインスタンスのプロパティの値を指定する場合は、します。 更新するときに、明示的な値を設定することもできます。 詳細については、次を参照してください。[生成されたプロパティの値を明示的](..\saving\explicit-values-generated-properties.md)です。

> [!WARNING]  
> 追加と更新されたエンティティの値を生成する方法は、使用中のデータベース プロバイダーによって異なります。 データベース プロバイダー、他のユーザーが必要とする値を生成する方法を手動でセットアップするプロパティの種類によっての値を生成が自動的にセットアップ可能性があります。
>
> たとえば、SQL Server を使用して`byte[]`で生成されるように設定されているプロパティの追加または更新、および環境でのセットアップになります、同時実行トークンとしてマークされている、`rowversion`データ型の値はデータベースで生成するようにします。 ただし、設定した場合、`DateTime`プロパティが生成されるで追加または更新の値を生成する方法をセットアップする必要があります。 既定値を構成するには、1 つの方法は、 `GETDATE()` (を参照してください[既定値](relational/default-values.md)) を新しい行の値を生成します。 データベース トリガーを使用して、(次の例のトリガー) などの更新時に値を生成する可能性があります。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>規則

規則では、整数または GUID データ型の主キーを追加で生成された値を持つセットアップとなります。 他のすべてのプロパティには、値を生成しないとセットアップはできます。

## <a name="data-annotations"></a>データの注釈

### <a name="no-value-generation-data-annotations"></a>値が (データ注釈) を生成しません。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>生成された値が (データ注釈) を追加します。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> これにより、EF を追加したエンティティの値が生成される、EF が値を生成する実際のメカニズムを設定することは保証されませんを理解ことだけです。 参照してください[で生成された値を追加](#value-generated-on-add)詳細についてはします。

### <a name="value-generated-on-add-or-update-data-annotations"></a>生成された値を追加または更新 (データ注釈)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、EF の追加または更新されたエンティティの値が生成されることは、EF が値を生成する実際のメカニズムを設定することは保証されませんを理解ことだけです。 参照してください[で生成された値を追加または更新](#value-generated-on-add-or-update)詳細についてはします。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、特定のプロパティの値の生成のパターンを変更することができます。

### <a name="no-value-generation-fluent-api"></a>値を生成しない (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>生成された値 (Fluent API) を追加します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()`EF を追加したエンティティの値が生成される、EF が値を生成する実際のメカニズムを設定することは保証されませんを理解したことができます。  参照してください[で生成された値を追加](#value-generated-on-add)詳細についてはします。

### <a name="value-generated-on-add-or-update-fluent-api"></a>生成された値を追加または更新 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> これにより、EF の追加または更新されたエンティティの値が生成されることは、EF が値を生成する実際のメカニズムを設定することは保証されませんを理解ことだけです。 参照してください[で生成された値を追加または更新](#value-generated-on-add-or-update)詳細についてはします。

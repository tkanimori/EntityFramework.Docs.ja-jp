---
title: エンティティのプロパティ-EF Core
description: Entity Framework Core を使用してエンティティのプロパティを構成およびマップする方法
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: b67603fbffd1f1c8506bc21f8972c851eb8eef29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414567"
---
# <a name="entity-properties"></a>エンティティのプロパティ

モデル内の各エンティティ型には、一連のプロパティがあります。これらのプロパティは、データベースから読み取りおよび書き込みを実行 EF Core ます。 リレーショナルデータベースを使用している場合、エンティティのプロパティはテーブルの列にマップされます。

## <a name="included-and-excluded-properties"></a>包含プロパティと除外されるプロパティ

慣例により、getter と setter を持つすべてのパブリックプロパティがモデルに含まれます。

特定のプロパティは、次のように除外できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>列名

規則により、リレーショナルデータベースを使用する場合、エンティティのプロパティは、プロパティと同じ名前のテーブル列にマップされます。

異なる名前で列を構成する場合は、次のようにします。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>列のデータ型

リレーショナルデータベースを使用する場合、データベースプロバイダーは、プロパティの .NET 型に基づいてデータ型を選択します。 また、構成された[最大長](#maximum-length)、プロパティが主キーの一部であるかどうかなど、他のメタデータも考慮する必要があります。

たとえば、SQL Server は `DateTime` プロパティを `datetime2(7)` 列にマップし、プロパティを `nvarchar(max)` 列 (またはキーとして使用されるプロパティの `nvarchar(450)`) にマップ `string` します。

列のデータ型を正確に指定するように列を構成することもできます。 たとえば、次のコードは、`200` の最大長を持つ非 unicode 文字列として `Url` を構成し、`2`の `5` と小数点以下桁数を使用して10進数として `Rating` します。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>最大の長さ

最大長を構成すると、特定のプロパティに対して選択する適切な列のデータ型について、データベースプロバイダーにヒントが提供されます。 最大長は、`string` や `byte[]`などの配列データ型にのみ適用されます。

> [!NOTE]
> Entity Framework は、データをプロバイダーに渡す前に、最大長の検証を行いません。 必要に応じて、プロバイダーまたはデータストアが検証する必要があります。 たとえば、SQL Server を対象とする場合、基になる列のデータ型では過剰なデータを格納できないため、最大長を超えると例外が発生します。

次の例では、最大長の500を構成すると、SQL Server に `nvarchar(500)` 型の列が作成されます。

#### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>必須および省略可能なプロパティ

プロパティは、`null`を格納するために有効であれば、省略可能と見なされます。 `null` がプロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。 リレーショナルデータベーススキーマにマップする場合、必要なプロパティは null 非許容列として作成され、オプションのプロパティは null 値が許容される列として作成されます。

### <a name="conventions"></a>規則

慣例により、.NET 型に null を含めることができるプロパティはオプションとして構成されますが、.NET 型に null を含めることができないプロパティは必要に応じて構成されます。 たとえば、.NET 値の種類 (`int`、`decimal`、`bool`など) を持つすべてのプロパティは必須として構成され、null 許容型の .NET 値型 (`int?`、`decimal?`、`bool?`など) を持つすべてのプロパティは省略可能として構成されます。

C#8では、null 値を[許容する参照](/dotnet/csharp/tutorials/nullable-reference-types)型と呼ばれる新しい機能が導入されています。これにより、参照型に注釈を付けて、null を含むかどうかを示すことができます。 この機能は既定で無効になっています。有効にすると、EF Core の動作が次のように変更されます。

* Null 許容の参照型が無効になっている場合 (既定)、.NET 参照型を持つすべてのプロパティは、規則によってオプションとして構成されます (例: `string`)。
* Null 値を許容する参照型が有効になっている場合C# 、プロパティは .net 型の null 値の許容属性に基づいて構成されます。 `string?` はオプションとして構成され、`string` は必須として構成されます。

次の例は、必須プロパティと省略可能なプロパティを持つエンティティ型を示しています。 null 許容の参照機能が無効 (既定値) で、有効になっています。

#### <a name="without-nullable-reference-types-default"></a>[Null 値を許容する参照型を使用しない場合 (既定)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Null 値を許容する参照型を持つ](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

コードでC#表現された null 値許容を EF Core のモデルとデータベースにフローし、Fluent API またはデータ注釈を使用して同じ概念を2回表現するため、null 許容型参照型を使用することをお勧めします。

> [!NOTE]
> 既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。 リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。

Null 許容型の参照型と EF Core での使用方法の詳細については、[この機能の専用ドキュメントに関するページを参照してください](xref:core/miscellaneous/nullable-reference-types)。

### <a name="explicit-configuration"></a>明示的な構成

規約によってオプションとなるプロパティは、次のように必要になるように構成できます。

#### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

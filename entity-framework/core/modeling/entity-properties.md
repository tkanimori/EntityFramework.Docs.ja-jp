---
title: エンティティのプロパティ-EF Core
description: Entity Framework Core を使用してエンティティのプロパティを構成およびマップする方法
author: lajones
ms.date: 05/27/2020
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: fcf3b0f8480fde2f3ba6b5fd601db115f1d246b8
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370514"
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

たとえば、SQL Server は、プロパティを列に、プロパティを列にマップし `DateTime` `datetime2(7)` `string` `nvarchar(max)` ます (または、 `nvarchar(450)` キーとして使用されるプロパティの場合)。

列のデータ型を正確に指定するように列を構成することもできます。 たとえば、次のコードは、の最大長を持つ非 unicode 文字列としてを構成し、の `Url` `200` `Rating` 有効桁数と小数点以下桁数を10進数に設定し `5` `2` ます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>最大長

最大長を構成すると、特定のプロパティに対して選択する適切な列のデータ型について、データベースプロバイダーにヒントが提供されます。 最大長は、配列のデータ型 (やなど) にのみ適用さ `string` `byte[]` れます。

> [!NOTE]
> Entity Framework は、データをプロバイダーに渡す前に、最大長の検証を行いません。 必要に応じて、プロバイダーまたはデータストアが検証する必要があります。 たとえば、SQL Server を対象とする場合、基になる列のデータ型では過剰なデータを格納できないため、最大長を超えると例外が発生します。

次の例では、最大長の500を構成すると、SQL Server に型の列が作成され `nvarchar(500)` ます。

#### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a>有効桁数と小数点以下桁数

EFCore 5.0 以降では、fluent API を使用して、有効桁数と小数点以下桁数を構成できます。 このメソッドは、特定の列に必要なストレージの量をデータベースプロバイダーに伝えます。 これは、プロバイダーが有効桁数と小数点以下桁数を変化させることができるデータ型にのみ適用されます (通常はとのみ) `decimal` `DateTime` 。

プロパティの場合 `decimal` 、有効桁数は列に含まれる任意の値を表すために必要な最大桁数を定義し、scale は必要な小数点以下の最大桁数を定義します。 プロパティの場合 `DateTime` 、有効桁数は秒の小数部を表すために必要な最大桁数を定義し、小数点以下桁数は使用されません。

> [!NOTE]
> Entity Framework は、データをプロバイダーに渡す前に、精度または小数点以下桁数の検証を行いません。 必要に応じて、プロバイダーまたはデータストアが検証します。 たとえば、SQL Server を対象とする場合、データ型の列では `datetime` 有効桁数を設定することはできませんが、1つのの有効桁数は 0 ~ 7 の範囲で指定 `datetime2` できます。

次の例では、 `Score` 有効桁数が14で、小数点以下桁数が2のプロパティを構成すると、SQL Server で型の列が作成され、 `decimal(14,2)` `LastUpdated` 有効桁数が3になるようにプロパティを構成すると、型の列が生成され `datetime2(3)` ます。

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> 小数点以下桁数が定義されていない場合は、小数点以下桁数を定義するための Fluent API が使用され `HasPrecision(precision, scale)` ます。

***

## <a name="required-and-optional-properties"></a>必須および省略可能なプロパティ

プロパティが有効である場合、プロパティは省略可能と見なされ `null` ます。 `null`がプロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。 リレーショナルデータベーススキーマにマップする場合、必要なプロパティは null 非許容列として作成され、オプションのプロパティは null 値が許容される列として作成されます。

### <a name="conventions"></a>規約

慣例により、.NET 型に null を含めることができるプロパティはオプションとして構成されますが、.NET 型に null を含めることができないプロパティは必要に応じて構成されます。 たとえば、.net 値の型 (、、など) を持つすべてのプロパティは必須として構成され、 `int` `decimal` `bool` null 許容型の .net 値型 (、、など) を持つすべてのプロパティ `int?` `decimal?` `bool?` はオプションとして構成されます。

C# 8 では、null[許容参照型](/dotnet/csharp/tutorials/nullable-reference-types)と呼ばれる新しい機能が導入されました。これにより、参照型に注釈を付けて、null を含むかどうかを示すことができます。 この機能は既定で無効になっています。有効にすると、EF Core の動作が次のように変更されます。

* Null 許容の参照型が無効になっている場合 (既定)、.NET 参照型を持つすべてのプロパティは、規約によってオプションとして構成されます (例: `string` )。
* Null 値を許容する参照型が有効になっている場合、プロパティは、その .NET 型の C# の null 値の許容属性に基づいて構成されます。 `string?` はオプションとして構成されますが、 `string` は必須として構成されます。

次の例は、必須プロパティと省略可能なプロパティを持つエンティティ型を示しています。 null 許容の参照機能が無効 (既定値) で、有効になっています。

#### <a name="without-nullable-reference-types-default"></a>[Null 値を許容する参照型を使用しない場合 (既定)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Null 値を許容する参照型を持つ](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

C# コードで表される null 値の許容を EF Core のモデルとデータベースにフローさせるため、null 許容型を使用することをお勧めします。これにより、Fluent API またはデータ注釈を使用して同じ概念を2回表現することがなくなります。

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

## <a name="column-collations"></a>列の照合順序

> [!NOTE]
> この機能は EF Core 5.0 で導入されています。

照合順序は、テキスト列に対して定義し、比較および順序付けの方法を決定できます。 たとえば、次の例では、SQL Server 列が大文字と小文字を区別しないように構成されています。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

データベース内のすべての列で特定の照合順序を使用する必要がある場合は、代わりにデータベースレベルで照合順序を定義します。

照合順序のサポート EF Core に関する一般情報については、[照合順序のドキュメントページ](xref:core/miscellaneous/collations-and-case-sensitivity)を参照してください。
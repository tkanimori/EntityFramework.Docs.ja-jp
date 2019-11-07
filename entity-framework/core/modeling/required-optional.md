---
title: 必須およびオプションのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 62b2b3f5a761c0aacece986ecd0b2dd2f958d048
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655664"
---
# <a name="required-and-optional-properties"></a>必須および任意のプロパティ

プロパティは、`null`を格納するために有効であれば、省略可能と見なされます。 `null` がプロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。

リレーショナルデータベーススキーマにマップする場合、必要なプロパティは null 非許容列として作成され、オプションのプロパティは null 値が許容される列として作成されます。

## <a name="conventions"></a>規約

慣例により、.NET 型に null を含めることができるプロパティはオプションとして構成されますが、.NET 型に null を含めることができないプロパティは必要に応じて構成されます。 たとえば、.NET 値の種類 (`int`、`decimal`、`bool`など) を持つすべてのプロパティは必須として構成され、null 許容型の .NET 値型 (`int?`、`decimal?`、`bool?`など) を持つすべてのプロパティは省略可能として構成されます。

C#8では、null 値を[許容する参照](/dotnet/csharp/tutorials/nullable-reference-types)型と呼ばれる新しい機能が導入されています。これにより、参照型に注釈を付けて、null を含むかどうかを示すことができます。 この機能は既定で無効になっています。有効にすると、EF Core の動作が次のように変更されます。

* Null 許容の参照型が無効になっている場合 (既定)、.NET 参照型を持つすべてのプロパティは、規則によってオプションとして構成されます (例: `string`)。
* Null 値を許容する参照型が有効になっている場合C# 、プロパティは .net 型の null 値の許容属性に基づいて構成されます。 `string?` はオプションとして構成され、`string` は必須として構成されます。

次の例は、必須プロパティと省略可能なプロパティを持つエンティティ型を示しています。 null 許容の参照機能が無効 (既定値) で、有効になっています。

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[Null 値を許容する参照型を使用しない場合 (既定)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[Null 値を許容する参照型を持つ](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

コードでC#表現された null 値許容を EF Core のモデルとデータベースにフローし、Fluent API またはデータ注釈を使用して同じ概念を2回表現するため、null 許容型参照型を使用することをお勧めします。

> [!NOTE]
> 既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。 リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。

Null 許容型の参照型と EF Core での使用方法の詳細については、[この機能の専用ドキュメントに関するページを参照してください](xref:core/miscellaneous/nullable-reference-types)。

## <a name="configuration"></a>構成

規約によってオプションとなるプロパティは、次のように必要になるように構成できます。

# <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***

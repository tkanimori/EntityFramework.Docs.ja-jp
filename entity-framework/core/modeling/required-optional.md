---
title: 必須およびオプションのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: fd9e96e6f79965e63b07c21217edd004fd5c4d54
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197844"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="30e79-102">必須および省略可能なプロパティ</span><span class="sxs-lookup"><span data-stu-id="30e79-102">Required and Optional Properties</span></span>

<span data-ttu-id="30e79-103">プロパティが有効`null`である場合、プロパティは省略可能と見なされます。</span><span class="sxs-lookup"><span data-stu-id="30e79-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="30e79-104">が`null`プロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="30e79-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

<span data-ttu-id="30e79-105">リレーショナルデータベーススキーマにマップする場合、必要なプロパティは null 非許容列として作成され、オプションのプロパティは null 値が許容される列として作成されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-105">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

## <a name="conventions"></a><span data-ttu-id="30e79-106">規約</span><span class="sxs-lookup"><span data-stu-id="30e79-106">Conventions</span></span>

<span data-ttu-id="30e79-107">慣例により、.NET 型に null を含めることができるプロパティはオプションとして構成されますが、.NET 型に null を含めることができないプロパティは必要に応じて構成されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-107">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="30e79-108">たとえば、.net 値の型 (`int` `bool`、 `decimal`、など) を持つすべてのプロパティは必須として構成され、null 許容型`bool?`の .net`int?`値`decimal?`型 (、、など) を持つすべてのプロパティは、オプションとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-108">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="30e79-109">C#8では、null 値を[許容する参照](/dotnet/csharp/tutorials/nullable-reference-types)型と呼ばれる新しい機能が導入されています。これにより、参照型に注釈を付けて、null を含むかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="30e79-109">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="30e79-110">この機能は既定で無効になっています。有効にすると、EF Core の動作が次のように変更されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-110">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="30e79-111">Null 許容の参照型が無効になっている場合 (既定)、.NET 参照型を持つすべてのプロパティは、 `string`規約によってオプションとして構成されます (例:)。</span><span class="sxs-lookup"><span data-stu-id="30e79-111">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="30e79-112">Null 値を許容する参照型が有効になっている場合C# 、プロパティは、その .net `string?`型の null 値の許容属性`string`に基づいて構成されます。はオプションとして構成されますが、は必須として構成されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-112">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="30e79-113">次の例は、必須プロパティと省略可能なプロパティを持つエンティティ型を示しています。 null 許容の参照機能が無効 (既定値) で、有効になっています。</span><span class="sxs-lookup"><span data-stu-id="30e79-113">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[<span data-ttu-id="30e79-114">Null 値を許容する参照型を使用しない場合 (既定)</span><span class="sxs-lookup"><span data-stu-id="30e79-114">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[<span data-ttu-id="30e79-115">Null 値を許容する参照型を持つ</span><span class="sxs-lookup"><span data-stu-id="30e79-115">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="30e79-116">コードでC#表現された null 値許容を EF Core のモデルとデータベースにフローし、Fluent API またはデータ注釈を使用して同じ概念を2回表現するため、null 許容型参照型を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="30e79-116">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="30e79-117">既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。</span><span class="sxs-lookup"><span data-stu-id="30e79-117">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="30e79-118">リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="30e79-118">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="30e79-119">Null 許容型の参照型と EF Core での使用方法の詳細については、[この機能の専用ドキュメントに関するページを参照してください](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="30e79-119">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

## <a name="configuration"></a><span data-ttu-id="30e79-120">構成</span><span class="sxs-lookup"><span data-stu-id="30e79-120">Configuration</span></span>

<span data-ttu-id="30e79-121">規約によってオプションとなるプロパティは、次のように必要になるように構成できます。</span><span class="sxs-lookup"><span data-stu-id="30e79-121">A property that would be optional by convention can be configured to be required as follows:</span></span>

# <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="30e79-122">データの注釈</span><span class="sxs-lookup"><span data-stu-id="30e79-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="30e79-123">Fluent API</span><span class="sxs-lookup"><span data-stu-id="30e79-123">Fluent API</span></span>](#tab/fluent-api) 

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***
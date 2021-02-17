---
title: エンティティのプロパティ-EF Core
description: Entity Framework Core を使用してエンティティのプロパティを構成およびマップする方法
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 3c64f5ac1c86a83b6456df9e29472dc0b22d8524
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543368"
---
# <a name="entity-properties"></a><span data-ttu-id="15166-103">エンティティのプロパティ</span><span class="sxs-lookup"><span data-stu-id="15166-103">Entity Properties</span></span>

<span data-ttu-id="15166-104">モデル内の各エンティティ型には、一連のプロパティがあります。これらのプロパティは、データベースから読み取りおよび書き込みを実行 EF Core ます。</span><span class="sxs-lookup"><span data-stu-id="15166-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="15166-105">リレーショナルデータベースを使用している場合、エンティティのプロパティはテーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="15166-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="15166-106">包含プロパティと除外されるプロパティ</span><span class="sxs-lookup"><span data-stu-id="15166-106">Included and excluded properties</span></span>

<span data-ttu-id="15166-107">慣例により、getter と setter を持つすべてのパブリックプロパティがモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="15166-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="15166-108">特定のプロパティは、次のように除外できます。</span><span class="sxs-lookup"><span data-stu-id="15166-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="15166-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="15166-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="15166-111">列名</span><span class="sxs-lookup"><span data-stu-id="15166-111">Column names</span></span>

<span data-ttu-id="15166-112">規則により、リレーショナルデータベースを使用する場合、エンティティのプロパティは、プロパティと同じ名前のテーブル列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="15166-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="15166-113">異なる名前で列を構成する場合は、次のコードスニペットのようにします。</span><span class="sxs-lookup"><span data-stu-id="15166-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="15166-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="15166-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="15166-116">列のデータ型</span><span class="sxs-lookup"><span data-stu-id="15166-116">Column data types</span></span>

<span data-ttu-id="15166-117">リレーショナルデータベースを使用する場合、データベースプロバイダーは、プロパティの .NET 型に基づいてデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="15166-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="15166-118">また、構成された [最大長](#maximum-length)、プロパティが主キーの一部であるかどうかなど、他のメタデータも考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15166-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="15166-119">たとえば、SQL Server は、プロパティを列に、プロパティを列にマップし `DateTime` `datetime2(7)` `string` `nvarchar(max)` ます (または、 `nvarchar(450)` キーとして使用されるプロパティの場合)。</span><span class="sxs-lookup"><span data-stu-id="15166-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="15166-120">列のデータ型を正確に指定するように列を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="15166-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="15166-121">たとえば、次のコードは、の最大長を持つ非 unicode 文字列としてを構成し、の `Url` `200` `Rating` 有効桁数と小数点以下桁数を10進数に設定し `5` `2` ます。</span><span class="sxs-lookup"><span data-stu-id="15166-121">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="15166-122">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=5,8)]

### <a name="fluent-api"></a>[<span data-ttu-id="15166-123">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=6-7)]

***

### <a name="maximum-length"></a><span data-ttu-id="15166-124">最大長</span><span class="sxs-lookup"><span data-stu-id="15166-124">Maximum length</span></span>

<span data-ttu-id="15166-125">最大長を構成すると、特定のプロパティに対して選択する適切な列のデータ型について、データベースプロバイダーにヒントが提供されます。</span><span class="sxs-lookup"><span data-stu-id="15166-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="15166-126">最大長は、配列のデータ型 (やなど) にのみ適用さ `string` `byte[]` れます。</span><span class="sxs-lookup"><span data-stu-id="15166-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="15166-127">Entity Framework は、データをプロバイダーに渡す前に、最大長の検証を行いません。</span><span class="sxs-lookup"><span data-stu-id="15166-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="15166-128">必要に応じて、プロバイダーまたはデータストアが検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15166-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="15166-129">たとえば、SQL Server を対象とする場合、基になる列のデータ型では過剰なデータを格納できないため、最大長を超えると例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="15166-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="15166-130">次の例では、最大長の500を構成すると、SQL Server に型の列が作成され `nvarchar(500)` ます。</span><span class="sxs-lookup"><span data-stu-id="15166-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="15166-131">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=5)]

#### <a name="fluent-api"></a>[<span data-ttu-id="15166-132">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a><span data-ttu-id="15166-133">有効桁数と小数点以下桁数</span><span class="sxs-lookup"><span data-stu-id="15166-133">Precision and Scale</span></span>

<span data-ttu-id="15166-134">EFCore 5.0 以降では、fluent API を使用して、有効桁数と小数点以下桁数を構成できます。</span><span class="sxs-lookup"><span data-stu-id="15166-134">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="15166-135">このメソッドは、特定の列に必要なストレージの量をデータベースプロバイダーに伝えます。</span><span class="sxs-lookup"><span data-stu-id="15166-135">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="15166-136">これは、プロバイダーが有効桁数と小数点以下桁数を変更できるデータ型にのみ適用されます (通常は `decimal` と) `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="15166-136">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="15166-137">プロパティの場合 `decimal` 、有効桁数は列に含まれる任意の値を表すために必要な最大桁数を定義し、scale は必要な小数点以下の最大桁数を定義します。</span><span class="sxs-lookup"><span data-stu-id="15166-137">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="15166-138">プロパティの場合 `DateTime` 、有効桁数は秒の小数部を表すために必要な最大桁数を定義し、小数点以下桁数は使用されません。</span><span class="sxs-lookup"><span data-stu-id="15166-138">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="15166-139">Entity Framework は、データをプロバイダーに渡す前に、精度または小数点以下桁数の検証を行いません。</span><span class="sxs-lookup"><span data-stu-id="15166-139">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="15166-140">必要に応じて、プロバイダーまたはデータストアが検証します。</span><span class="sxs-lookup"><span data-stu-id="15166-140">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="15166-141">たとえば、SQL Server を対象とする場合、データ型の列では `datetime` 有効桁数を設定することはできませんが、1つのの有効桁数は 0 ~ 7 の範囲で指定 `datetime2` できます。</span><span class="sxs-lookup"><span data-stu-id="15166-141">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="15166-142">次の例では、 `Score` 有効桁数が14で、小数点以下桁数が2のプロパティを構成すると、SQL Server で型の列が作成され、 `decimal(14,2)` `LastUpdated` 有効桁数が3になるようにプロパティを構成すると、型の列が生成され `datetime2(3)` ます。</span><span class="sxs-lookup"><span data-stu-id="15166-142">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="15166-143">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-143">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="15166-144">現在、データ注釈を使用して有効桁数と小数点以下桁数を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="15166-144">Precision and scale cannot currently be configured via data annotations.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="15166-145">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-145">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="15166-146">小数点以下桁数が定義されていない場合は、小数点以下桁数を定義するための Fluent API が使用され `HasPrecision(precision, scale)` ます。</span><span class="sxs-lookup"><span data-stu-id="15166-146">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="15166-147">必須および省略可能なプロパティ</span><span class="sxs-lookup"><span data-stu-id="15166-147">Required and optional properties</span></span>

<span data-ttu-id="15166-148">プロパティが有効である場合、プロパティは省略可能と見なされ `null` ます。</span><span class="sxs-lookup"><span data-stu-id="15166-148">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="15166-149">`null`がプロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="15166-149">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="15166-150">リレーショナルデータベーススキーマにマップする場合、必要なプロパティは null 非許容列として作成され、オプションのプロパティは null 値が許容される列として作成されます。</span><span class="sxs-lookup"><span data-stu-id="15166-150">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="15166-151">規約</span><span class="sxs-lookup"><span data-stu-id="15166-151">Conventions</span></span>

<span data-ttu-id="15166-152">慣例により、.NET 型に null を含めることができるプロパティはオプションとして構成されますが、.NET 型に null を含めることができないプロパティは必要に応じて構成されます。</span><span class="sxs-lookup"><span data-stu-id="15166-152">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="15166-153">たとえば、.net 値の型 (、、など) を持つすべてのプロパティは必須として構成され、 `int` `decimal` `bool` null 許容型の .net 値型 (、、など) を持つすべてのプロパティ `int?` `decimal?` `bool?` はオプションとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="15166-153">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="15166-154">C# 8 では、null [許容参照型 (NRT)](/dotnet/csharp/tutorials/nullable-reference-types)と呼ばれる新しい機能が導入されました。これにより、参照型に注釈を付け、null を含むかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="15166-154">C# 8 introduced a new feature called [nullable reference types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="15166-155">この機能は既定で無効になっており、次のように EF Core の動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="15166-155">This feature is disabled by default, and affects EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="15166-156">Null 許容の参照型が無効になっている場合 (既定)、.NET 参照型を持つすべてのプロパティは、規約によってオプションとして構成されます (たとえば、 `string` )。</span><span class="sxs-lookup"><span data-stu-id="15166-156">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="15166-157">Null 値を許容する参照型が有効になっている場合、プロパティは、その .NET 型の C# の null 値の許容属性に基づいて構成されます。 `string?` オプションとして構成されますが、必要に応じて構成されます `string` 。</span><span class="sxs-lookup"><span data-stu-id="15166-157">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="15166-158">次の例は、必須プロパティと省略可能なプロパティを持つエンティティ型を示しています。 null 許容の参照機能が無効 (既定値) で、有効になっています。</span><span class="sxs-lookup"><span data-stu-id="15166-158">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nrt-default"></a>[<span data-ttu-id="15166-159">NRT なし (既定)</span><span class="sxs-lookup"><span data-stu-id="15166-159">Without NRT (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=5,8)]

#### <a name="with-nrt"></a>[<span data-ttu-id="15166-160">NRT を使用</span><span class="sxs-lookup"><span data-stu-id="15166-160">With NRT</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="15166-161">C# コードで表される null 値の許容を EF Core のモデルとデータベースにフローさせるため、null 許容型を使用することをお勧めします。これにより、Fluent API またはデータ注釈を使用して同じ概念を2回表現することがなくなります。</span><span class="sxs-lookup"><span data-stu-id="15166-161">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="15166-162">既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。</span><span class="sxs-lookup"><span data-stu-id="15166-162">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="15166-163">リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="15166-163">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="15166-164">Null 許容型の参照型と EF Core での使用方法の詳細については、 [この機能の専用ドキュメントに関するページを参照してください](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="15166-164">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="15166-165">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="15166-165">Explicit configuration</span></span>

<span data-ttu-id="15166-166">規約によってオプションとなるプロパティは、次のように必要になるように構成できます。</span><span class="sxs-lookup"><span data-stu-id="15166-166">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="15166-167">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-167">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=5)]

#### <a name="fluent-api"></a>[<span data-ttu-id="15166-168">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-168">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="15166-169">列の照合順序</span><span class="sxs-lookup"><span data-stu-id="15166-169">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="15166-170">この機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="15166-170">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="15166-171">照合順序は、テキスト列に対して定義し、比較および順序付けの方法を決定できます。</span><span class="sxs-lookup"><span data-stu-id="15166-171">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="15166-172">たとえば、次のコードスニペットでは、SQL Server 列が大文字と小文字を区別しないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="15166-172">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=ColumnCollation)]

<span data-ttu-id="15166-173">データベース内のすべての列で特定の照合順序を使用する必要がある場合は、代わりにデータベースレベルで照合順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="15166-173">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="15166-174">照合順序のサポート EF Core に関する一般情報については、 [照合順序のドキュメントページ](xref:core/miscellaneous/collations-and-case-sensitivity)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15166-174">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="column-comments"></a><span data-ttu-id="15166-175">列のコメント</span><span class="sxs-lookup"><span data-stu-id="15166-175">Column comments</span></span>

<span data-ttu-id="15166-176">データベース列に対して設定された任意のテキストコメントを設定して、データベース内のスキーマをドキュメント化できます。</span><span class="sxs-lookup"><span data-stu-id="15166-176">You can set an arbitrary text comment that gets set on the database column, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="15166-177">データの注釈</span><span class="sxs-lookup"><span data-stu-id="15166-177">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="15166-178">データ注釈を使用したコメントの設定は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="15166-178">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnComment.cs?name=ColumnComment&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="15166-179">Fluent API</span><span class="sxs-lookup"><span data-stu-id="15166-179">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnComment.cs?name=ColumnComment&highlight=5)]

***

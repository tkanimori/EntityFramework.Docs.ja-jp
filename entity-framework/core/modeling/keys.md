---
title: キー-EF Core
description: Entity Framework Core を使用する場合のエンティティ型のキーの構成方法
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: 805396a13227aa62ed86ac17c742d055d7a22bbf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129188"
---
# <a name="keys"></a><span data-ttu-id="ea53d-103">[キー]</span><span class="sxs-lookup"><span data-stu-id="ea53d-103">Keys</span></span>

<span data-ttu-id="ea53d-104">キーは、各エンティティインスタンスの一意の識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="ea53d-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="ea53d-105">EF のほとんどのエンティティには、リレーショナルデータベースの *主キー* の概念にマップされる1つのキーがあります (キーのないエンティティについては、「 [キーなしエンティティ](xref:core/modeling/keyless-entity-types)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="ea53d-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="ea53d-106">エンティティには、主キーを超える追加のキーを含めることができます (詳細については、「 [代替キー](#alternate-keys) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="ea53d-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

<span data-ttu-id="ea53d-107">慣例により、またはという名前のプロパティは、 `Id` `<type name>Id` エンティティの主キーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-107">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="ea53d-108">所有されている[エンティティ型](xref:core/modeling/owned-entities)は、キーを定義するために異なる規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea53d-108">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="ea53d-109">次のように、1つのプロパティをエンティティの主キーとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-109">You can configure a single property to be the primary key of an entity as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="ea53d-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="ea53d-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[<span data-ttu-id="ea53d-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ea53d-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="ea53d-112">また、エンティティのキーとして複数のプロパティを構成することもできます。これは、複合キーと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-112">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="ea53d-113">複合キーは、Fluent API を使用してのみ構成できます。規則では、複合キーは設定されず、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="ea53d-113">Composite keys can only be configured using the Fluent API; conventions will never set up a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a><span data-ttu-id="ea53d-114">主キー名</span><span class="sxs-lookup"><span data-stu-id="ea53d-114">Primary key name</span></span>

<span data-ttu-id="ea53d-115">慣例により、リレーショナルデータベースでは、という名前の主キーが作成され `PK_<type name>` ます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-115">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="ea53d-116">次のように、primary key 制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-116">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="ea53d-117">キーの種類と値</span><span class="sxs-lookup"><span data-stu-id="ea53d-117">Key types and values</span></span>

<span data-ttu-id="ea53d-118">EF Core は、、などを含む任意のプリミティブ型のプロパティを主キーとして使用できますが、すべての `string` `Guid` データベースが `byte[]` キーとしてすべての型をサポートするわけではありません。</span><span class="sxs-lookup"><span data-stu-id="ea53d-118">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="ea53d-119">場合によっては、キー値がサポートされている型に自動的に変換されることがあります。それ以外の場合は、 [手動で](xref:core/modeling/value-conversions)変換を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ea53d-119">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="ea53d-120">新しいエンティティをコンテキストに追加する場合、キープロパティには既定値以外の値が常に設定されている必要がありますが、 [データベースによって生成](xref:core/modeling/generated-properties)される型もあります。</span><span class="sxs-lookup"><span data-stu-id="ea53d-120">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="ea53d-121">その場合、EF は、追跡のためにエンティティが追加されると、一時値の生成を試みます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-121">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="ea53d-122">[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)が呼び出された後、一時的な値はデータベースによって生成される値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-122">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="ea53d-123">キープロパティの値がデータベースによって生成され、エンティティの追加時に既定値以外の値が指定されている場合、EF は、そのエンティティがデータベースに既に存在していると見なし、新しいエンティティを挿入する代わりに更新しようとします。</span><span class="sxs-lookup"><span data-stu-id="ea53d-123">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="ea53d-124">これを回避するには、値の生成をオフにするか、 [生成されたプロパティに明示的な値を指定する方法](xref:core/saving/explicit-values-generated-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea53d-124">To avoid this turn off value generation or see [how to specify explicit values for generated properties](xref:core/saving/explicit-values-generated-properties).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="ea53d-125">代替キー</span><span class="sxs-lookup"><span data-stu-id="ea53d-125">Alternate Keys</span></span>

<span data-ttu-id="ea53d-126">代替キーは、主キーに加えて、各エンティティインスタンスの代替一意識別子として機能します。これは、リレーションシップのターゲットとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-126">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="ea53d-127">リレーショナルデータベースを使用する場合、これは代替キー列の一意のインデックス/制約の概念と、その列を参照する1つ以上の foreign key 制約にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-127">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="ea53d-128">列に一意性を適用するだけの場合は、代替キーではなく一意のインデックスを定義します (「 [インデックス](xref:core/modeling/indexes)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="ea53d-128">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](xref:core/modeling/indexes)).</span></span> <span data-ttu-id="ea53d-129">EF では、代替キーは読み取り専用で、一意のインデックスに対する追加のセマンティクスを提供します。これは、外部キーのターゲットとして使用できるためです。</span><span class="sxs-lookup"><span data-stu-id="ea53d-129">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="ea53d-130">通常、代替キーは必要に応じて導入され、手動で構成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ea53d-130">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="ea53d-131">慣例により、リレーションシップのターゲットとして主キーではないプロパティを特定するときに、代替キーが導入されます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-131">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="ea53d-132">また、1つのプロパティを別のキーにするように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-132">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="ea53d-133">また、複数のプロパティを別のキー (複合代替キーと呼ばれます) として構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-133">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="ea53d-134">最後に、規則により、代替キーに対して導入されるインデックスと制約にはという名前が付けられ `AK_<type name>_<property name>` ます (複合代替キーの場合は、アンダースコアで区切られた `<property name>` プロパティ名の一覧になります)。</span><span class="sxs-lookup"><span data-stu-id="ea53d-134">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="ea53d-135">代替キーのインデックスと unique 制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ea53d-135">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]

---
title: キー (プライマリ)-EF Core
description: Entity Framework Core を使用する場合のエンティティ型のキーの構成方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824615"
---
# <a name="keys-primary"></a><span data-ttu-id="01540-103">キー (主)</span><span class="sxs-lookup"><span data-stu-id="01540-103">Keys (primary)</span></span>

<span data-ttu-id="01540-104">キーは、各エンティティインスタンスのプライマリ一意識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="01540-104">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="01540-105">リレーショナルデータベースを使用する場合、これは*主キー*の概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="01540-105">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="01540-106">また、主キーではない一意の識別子を構成することもできます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="01540-106">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="01540-107">次のいずれかの方法を使用して、主キーの設定/作成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="01540-107">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="01540-108">規約</span><span class="sxs-lookup"><span data-stu-id="01540-108">Conventions</span></span>

<span data-ttu-id="01540-109">既定では、`Id` または `<type name>Id` という名前のプロパティは、エンティティのキーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="01540-109">By default, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> <span data-ttu-id="01540-110">所有されている[エンティティ型](xref:core/modeling/owned-entities)は、キーを定義するために異なる規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="01540-110">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="01540-111">データの注釈</span><span class="sxs-lookup"><span data-stu-id="01540-111">Data Annotations</span></span>

<span data-ttu-id="01540-112">データ注釈を使用して、1つのプロパティをエンティティのキーとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="01540-112">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="01540-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="01540-113">Fluent API</span></span>

<span data-ttu-id="01540-114">Fluent API を使用して、1つのプロパティをエンティティのキーとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="01540-114">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="01540-115">また、Fluent API を使用して、エンティティ (複合キーと呼ばれます) のキーとして複数のプロパティを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="01540-115">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="01540-116">複合キーは、Fluent API を使用してのみ構成できます。複合キーは設定されず、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="01540-116">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a><span data-ttu-id="01540-117">キーの種類と値</span><span class="sxs-lookup"><span data-stu-id="01540-117">Key types and values</span></span>

<span data-ttu-id="01540-118">EF Core では、`string`、`Guid`、`byte[]` など、任意のプリミティブ型のプロパティを主キーとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="01540-118">EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others.</span></span> <span data-ttu-id="01540-119">ただし、すべてのデータベースでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="01540-119">But not all databases support them.</span></span> <span data-ttu-id="01540-120">場合によっては、キー値がサポートされている型に自動的に変換されることがあります。それ以外の場合は、[手動で](xref:core/modeling/value-conversions)変換を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01540-120">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="01540-121">新しいエンティティをコンテキストに追加する場合、キープロパティには既定値以外の値が常に設定されている必要がありますが、[データベースによって生成](xref:core/modeling/generated-properties)される型もあります。</span><span class="sxs-lookup"><span data-stu-id="01540-121">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="01540-122">その場合、EF は、追跡のためにエンティティが追加されると、一時値の生成を試みます。</span><span class="sxs-lookup"><span data-stu-id="01540-122">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="01540-123">[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)が呼び出された後、一時的な値はデータベースによって生成される値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="01540-123">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="01540-124">キープロパティにデータベースによって生成された値があり、エンティティの追加時に既定以外の値が指定されている場合、EF はそのエンティティがデータベースに既に存在していると見なし、新しいエンティティを挿入する代わりに更新しようとします。</span><span class="sxs-lookup"><span data-stu-id="01540-124">If a key property has value generated by the database and a non-default value is specified when an entity is added then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="01540-125">これを回避するには、値の生成をオフにするか、[生成されたプロパティに明示的な値を指定する方法](../saving/explicit-values-generated-properties.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01540-125">To avoid this turn off value generation or see [how to specify explicit values for generated properties](../saving/explicit-values-generated-properties.md).</span></span>
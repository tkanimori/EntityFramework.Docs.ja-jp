---
title: バッキングフィールド-EF Core
description: Entity Framework Core モデルでのプロパティのバッキングフィールドの構成
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 36fc64efa08f2cdeb7005e3fc6335298f2426bde
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617577"
---
# <a name="backing-fields"></a><span data-ttu-id="d92c3-103">バッキング フィールド</span><span class="sxs-lookup"><span data-stu-id="d92c3-103">Backing Fields</span></span>

<span data-ttu-id="d92c3-104">バッキングフィールドを使用すると、EF はプロパティではなくフィールドに対して読み取りや書き込みを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="d92c3-105">これは、クラス内のカプセル化を使用して、アプリケーションコードによるデータへのアクセスを制限したり、データへのアクセスを制限したりする場合に便利ですが、このような制限や機能強化を使用せずに、データベースに対して値の読み取りや書き込みを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d92c3-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="d92c3-106">基本構成</span><span class="sxs-lookup"><span data-stu-id="d92c3-106">Basic configuration</span></span>

<span data-ttu-id="d92c3-107">慣例により、次のフィールドは、特定のプロパティのバッキングフィールドとして検出されます (優先順位順に一覧表示されます)。</span><span class="sxs-lookup"><span data-stu-id="d92c3-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="d92c3-108">次の例では、 `Url` プロパティはバッキングフィールドとしてを持つように構成されてい `_url` ます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-108">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="d92c3-109">バッキングフィールドは、モデルに含まれているプロパティに対してのみ検出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d92c3-109">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="d92c3-110">モデルに含まれるプロパティの詳細については、「 [プロパティを除外 & を含める](xref:core/modeling/entity-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d92c3-110">For more information on which properties are included in the model, see [Including & Excluding Properties](xref:core/modeling/entity-properties).</span></span>

<span data-ttu-id="d92c3-111">データ注釈 (EFCore 5.0 で利用可能) または Fluent API を使用してバッキングフィールドを構成することもできます。たとえば、フィールド名が上記の規則に対応していない場合です。</span><span class="sxs-lookup"><span data-stu-id="d92c3-111">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d92c3-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="d92c3-112">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="d92c3-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d92c3-113">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="d92c3-114">フィールドアクセスとプロパティアクセス</span><span class="sxs-lookup"><span data-stu-id="d92c3-114">Field and property access</span></span>

<span data-ttu-id="d92c3-115">既定では、EF は、バッキングフィールドが適切に構成されていることを前提として常に読み取りと書き込みを行います。プロパティは使用されません。</span><span class="sxs-lookup"><span data-stu-id="d92c3-115">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="d92c3-116">ただし、EF では他のアクセスパターンもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d92c3-116">However, EF also supports other access patterns.</span></span> <span data-ttu-id="d92c3-117">たとえば、次の例では、を具体化するときにのみバッキングフィールドに書き込むように EF に指示し、その他のすべての場合にプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="d92c3-117">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="d92c3-118">サポートされているオプションの完全なセットについては、「 [Propertyaccessmode 列挙型](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d92c3-118">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="d92c3-119">EF Core 3.0 では、既定のプロパティアクセスモードはからに変更されました `PreferFieldDuringConstruction` `PreferField` 。</span><span class="sxs-lookup"><span data-stu-id="d92c3-119">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="d92c3-120">フィールドのみのプロパティ</span><span class="sxs-lookup"><span data-stu-id="d92c3-120">Field-only properties</span></span>

<span data-ttu-id="d92c3-121">また、エンティティクラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成することもできますが、代わりにフィールドを使用してエンティティにデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="d92c3-121">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="d92c3-122">これは、データがエンティティの CLR 型ではなく、変更トラッカーに格納される [シャドウプロパティ](xref:core/modeling/shadow-properties)とは異なります。</span><span class="sxs-lookup"><span data-stu-id="d92c3-122">This is different from [Shadow Properties](xref:core/modeling/shadow-properties), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="d92c3-123">フィールドのみのプロパティは、一般に、エンティティクラスがプロパティの代わりにメソッドを使用して値を取得または設定する場合、またはフィールドがドメインモデル内でまったく公開されない場合 (主キーなど) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-123">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="d92c3-124">API に名前を指定することで、フィールドのみのプロパティを構成でき `Property(...)` ます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-124">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="d92c3-125">EF は、指定された名前を持つ CLR プロパティを見つけようとします。プロパティが見つからない場合は、フィールドを検索します。</span><span class="sxs-lookup"><span data-stu-id="d92c3-125">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="d92c3-126">プロパティもフィールドも見つからない場合は、代わりに shadow プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-126">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="d92c3-127">LINQ クエリからはフィールドのみのプロパティを参照する必要がありますが、このようなフィールドは通常はプライベートです。</span><span class="sxs-lookup"><span data-stu-id="d92c3-127">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="d92c3-128">`EF.Property(...)`LINQ クエリでメソッドを使用して、フィールドを参照できます。</span><span class="sxs-lookup"><span data-stu-id="d92c3-128">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

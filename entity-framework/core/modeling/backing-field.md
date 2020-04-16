---
title: バッキング フィールド - EF コア
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434176"
---
# <a name="backing-fields"></a><span data-ttu-id="b4c86-102">バッキング フィールド</span><span class="sxs-lookup"><span data-stu-id="b4c86-102">Backing Fields</span></span>

<span data-ttu-id="b4c86-103">バッキング フィールドを使用すると、EF はプロパティではなくフィールドの読み取りおよび書き込みを行えます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="b4c86-104">これは、クラス内のカプセル化を使用して、アプリケーション コードによるデータへのアクセスに関するセマンティクスの使用を制限したり、そのいずれかを強化したりするときに役立ちますが、これらの制限や拡張を使用せずに値をデータベースから読み取ったり、データベースに書き込んだりする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4c86-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="b4c86-105">基本構成</span><span class="sxs-lookup"><span data-stu-id="b4c86-105">Basic configuration</span></span>

<span data-ttu-id="b4c86-106">規則により、次のフィールドは、特定のプロパティのバッキング フィールドとして検出されます (優先順位に示されています)。</span><span class="sxs-lookup"><span data-stu-id="b4c86-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="b4c86-107">次の例では、プロパティ`Url`がバッキング フィールド`_url`として設定されています。</span><span class="sxs-lookup"><span data-stu-id="b4c86-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="b4c86-108">バッキング フィールドは、モデルに含まれるプロパティに対してのみ検出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b4c86-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="b4c86-109">モデルに含まれるプロパティの詳細については、「プロパティを[除外する&を含める](included-properties.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4c86-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="b4c86-110">バッキング フィールドは、データ注釈や Fluent API を使用して構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b4c86-111">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b4c86-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="b4c86-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b4c86-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="b4c86-113">フィールドとプロパティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="b4c86-113">Field and property access</span></span>

<span data-ttu-id="b4c86-114">既定では、EF は常にバッキング フィールドに対して読み取りと書き込みを行います ( 適切に構成されていると仮定 ) 、 プロパティを使用することはありません。</span><span class="sxs-lookup"><span data-stu-id="b4c86-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="b4c86-115">ただし、EF は他のアクセス パターンもサポートしています。</span><span class="sxs-lookup"><span data-stu-id="b4c86-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="b4c86-116">たとえば、次のサンプルは、具体化中にのみバッキング フィールドに書き込み、その他すべての場合にプロパティを使用するように EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="b4c86-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="b4c86-117">サポートされているオプションの完全なセットについては、[プロパティアクセス モード列挙型](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4c86-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c86-118">EF Core 3.0 では、既定のプロパティ`PreferFieldDuringConstruction`アクセス`PreferField`モードがから に変更されました。</span><span class="sxs-lookup"><span data-stu-id="b4c86-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="b4c86-119">フィールドのみのプロパティ</span><span class="sxs-lookup"><span data-stu-id="b4c86-119">Field-only properties</span></span>

<span data-ttu-id="b4c86-120">エンティティ クラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成し、代わりにフィールドを使用してエンティティにデータを格納することもできます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="b4c86-121">これは、エンティティの CLR 型ではなく、変更トラッカーにデータが格納される[シャドウ プロパティ](shadow-properties.md)とは異なります。</span><span class="sxs-lookup"><span data-stu-id="b4c86-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="b4c86-122">フィールドのみのプロパティは、エンティティ クラスがプロパティではなくメソッドを使用して値を取得または設定する場合、またはドメイン モデルでフィールドをまったく公開しない場合 (主キーなど) で一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="b4c86-123">API で名前を指定することで、フィールドのみのプロパティを`Property(...)`構成できます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="b4c86-124">EF は、指定された名前の CLR プロパティ、またはプロパティが見つからない場合はフィールドを検索しようとします。</span><span class="sxs-lookup"><span data-stu-id="b4c86-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="b4c86-125">プロパティもフィールドも見つからない場合は、シャドウ プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="b4c86-126">LINQ クエリからフィールドのみのプロパティを参照する必要がある場合がありますが、このようなフィールドは通常プライベートです。</span><span class="sxs-lookup"><span data-stu-id="b4c86-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="b4c86-127">このメソッドを`EF.Property(...)`LINQ クエリで使用して、フィールドを参照できます。</span><span class="sxs-lookup"><span data-stu-id="b4c86-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

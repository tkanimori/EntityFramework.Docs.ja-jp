---
title: バッキングフィールド-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: c3ca8bb97992c192672e8c2f2040b0de029df68d
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197484"
---
# <a name="backing-fields"></a><span data-ttu-id="06913-102">バッキング フィールド</span><span class="sxs-lookup"><span data-stu-id="06913-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="06913-103">この機能は EF Core 1.1 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="06913-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="06913-104">バッキングフィールドを使用すると、EF はプロパティではなくフィールドに対して読み取りや書き込みを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="06913-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="06913-105">これは、クラスのカプセル化を使用して、アプリケーションコードによるデータへのアクセスを制限したり、データへのアクセスを制限したりする場合に便利ですが、値は、これらの制限を使用せずに、データベースに対して読み取りや書き込みを行う必要があります。増強.</span><span class="sxs-lookup"><span data-stu-id="06913-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="06913-106">規約</span><span class="sxs-lookup"><span data-stu-id="06913-106">Conventions</span></span>

<span data-ttu-id="06913-107">慣例により、次のフィールドは、特定のプロパティのバッキングフィールドとして検出されます (優先順位順に一覧表示されます)。</span><span class="sxs-lookup"><span data-stu-id="06913-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="06913-108">フィールドは、モデルに含まれているプロパティに対してのみ検出されます。</span><span class="sxs-lookup"><span data-stu-id="06913-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="06913-109">モデルに含まれるプロパティの詳細については、「[プロパティを除外 & を含める](included-properties.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="06913-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="06913-110">バッキングフィールドが構成されている場合、EF は、プロパティ setter を使用するのではなく、データベースからエンティティインスタンスを具体化するときに、そのフィールドに直接書き込みます。</span><span class="sxs-lookup"><span data-stu-id="06913-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="06913-111">EF が他のタイミングで値の読み取りまたは書き込みを行う必要がある場合は、可能であればプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="06913-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="06913-112">たとえば、EF がプロパティの値を更新する必要がある場合、プロパティ set アクセス操作子が定義されていれば、そのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="06913-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="06913-113">プロパティが読み取り専用の場合は、フィールドに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="06913-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="06913-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="06913-114">Data Annotations</span></span>

<span data-ttu-id="06913-115">データ注釈を使用してバッキングフィールドを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="06913-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="06913-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="06913-116">Fluent API</span></span>

<span data-ttu-id="06913-117">Fluent API を使用して、プロパティのバッキングフィールドを構成できます。</span><span class="sxs-lookup"><span data-stu-id="06913-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="06913-118">フィールドが使用されるタイミングの制御</span><span class="sxs-lookup"><span data-stu-id="06913-118">Controlling when the field is used</span></span>

<span data-ttu-id="06913-119">EF がフィールドまたはプロパティを使用するタイミングを構成できます。</span><span class="sxs-lookup"><span data-stu-id="06913-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="06913-120">サポートされているオプションについては、「 [Propertyaccessmode 列挙型](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="06913-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="06913-121">プロパティのないフィールド</span><span class="sxs-lookup"><span data-stu-id="06913-121">Fields without a property</span></span>

<span data-ttu-id="06913-122">また、エンティティクラスに対応する CLR プロパティを持たない概念プロパティをモデルに作成することもできますが、代わりにフィールドを使用してエンティティにデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="06913-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="06913-123">これは、データが変更トラッカーに格納される[シャドウプロパティ](shadow-properties.md)とは異なります。</span><span class="sxs-lookup"><span data-stu-id="06913-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="06913-124">これは通常、エンティティクラスがメソッドを使用して値を取得/設定する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="06913-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="06913-125">EF には、 `Property(...)` API 内のフィールドの名前を指定できます。</span><span class="sxs-lookup"><span data-stu-id="06913-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="06913-126">指定された名前のプロパティが存在しない場合、EF はフィールドを検索します。</span><span class="sxs-lookup"><span data-stu-id="06913-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="06913-127">フィールド名以外の名前をプロパティに指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="06913-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="06913-128">この名前は、モデルを作成するときに使用されます。特に、データベース内のにマップされている列名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="06913-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="06913-129">エンティティクラスにプロパティがない場合は、LINQ クエリで`EF.Property(...)`メソッドを使用して、モデルの概念の一部であるプロパティを参照できます。</span><span class="sxs-lookup"><span data-stu-id="06913-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

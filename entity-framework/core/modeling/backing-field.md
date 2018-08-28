---
title: バッキング フィールド - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 79221b6f7968675ff10f80d5df181b674b6a20c9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994094"
---
# <a name="backing-fields"></a><span data-ttu-id="c1f76-102">バッキング フィールド</span><span class="sxs-lookup"><span data-stu-id="c1f76-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="c1f76-103">この機能は、EF Core 1.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="c1f76-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="c1f76-104">バッキング フィールドは、読み取りまたは書き込みのプロパティではなく、フィールドに EF を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="c1f76-105">これは、便利な場合、クラスにカプセル化は、アプリケーション コードでの使用を制限したり、データへのアクセスのセマンティクスの向上に使用されているが、値を読み取ったりやこれらの制限事項を使用せずに、データベースに書き込まれる必要があります/機能強化。</span><span class="sxs-lookup"><span data-stu-id="c1f76-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="c1f76-106">規約</span><span class="sxs-lookup"><span data-stu-id="c1f76-106">Conventions</span></span>

<span data-ttu-id="c1f76-107">慣例により、バッキング フィールド (優先順位の順序で表示されている) 特定のプロパティとして、次のフィールドが検出されます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="c1f76-108">フィールドは、モデルに含まれているプロパティにのみ検出されます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="c1f76-109">プロパティがモデルに含まれる詳細については、次を参照してください。[含める/除外するプロパティ](included-properties.md)します。</span><span class="sxs-lookup"><span data-stu-id="c1f76-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

<span data-ttu-id="c1f76-110">バッキング フィールドが構成されている場合、EF は、(プロパティの set アクセス操作子を使用するのではなく) データベースからエンティティのインスタンスを具体化する場合にそのフィールドに直接書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="c1f76-111">EF の読み取りまたはそれ以外の時間の値を上書きする場合、可能であれば、プロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="c1f76-112">たとえば、EF では、プロパティの値を更新する必要がある場合が定義されている場合、プロパティ set アクセス操作子を使用、されます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="c1f76-113">プロパティが読み取り専用の場合は、フィールドに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c1f76-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c1f76-114">Data Annotations</span></span>

<span data-ttu-id="c1f76-115">バッキング フィールドは、データ注釈で構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c1f76-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c1f76-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c1f76-116">Fluent API</span></span>

<span data-ttu-id="c1f76-117">Fluent API を使用して、プロパティのバッキング フィールドを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="c1f76-118">フィールドを使用する場合を制御します。</span><span class="sxs-lookup"><span data-stu-id="c1f76-118">Controlling when the field is used</span></span>

<span data-ttu-id="c1f76-119">EF は、フィールドまたはプロパティを使用する場合を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="c1f76-120">参照してください、 [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)のサポートされるオプション。</span><span class="sxs-lookup"><span data-stu-id="c1f76-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="c1f76-121">プロパティ フィールド</span><span class="sxs-lookup"><span data-stu-id="c1f76-121">Fields without a property</span></span>

<span data-ttu-id="c1f76-122">エンティティ クラスに対応する CLR プロパティはありませんが、代わりに、エンティティにデータを格納するフィールドを使用するモデルの概念のプロパティを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="c1f76-123">これは、異なる[シャドウ プロパティ](shadow-properties.md)、変更トラッカーにデータを格納する場所。</span><span class="sxs-lookup"><span data-stu-id="c1f76-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="c1f76-124">これは通常、使用、エンティティ クラスでは、メソッドを使用して、値を取得または設定する場合。</span><span class="sxs-lookup"><span data-stu-id="c1f76-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="c1f76-125">EF のフィールドの名前を指定できます、 `Property(...)` API。</span><span class="sxs-lookup"><span data-stu-id="c1f76-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="c1f76-126">指定した名前のプロパティがない場合は、EF はフィールドになります。</span><span class="sxs-lookup"><span data-stu-id="c1f76-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="c1f76-127">プロパティ、フィールド名以外の名前を選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="c1f76-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="c1f76-128">この名前は、モデルを作成するときに使用し、データベース内にマップされている列名を使用する最も顕著な。</span><span class="sxs-lookup"><span data-stu-id="c1f76-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="c1f76-129">エンティティ クラスのプロパティがない、ときに使用できます、`EF.Property(...)`メソッドで LINQ クエリを概念的には、モデルの一部であるプロパティを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1f76-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

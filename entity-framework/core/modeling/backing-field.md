---
title: "EF コアをバックアップするには、フィールド-"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
ms.technology: entity-framework-core
uid: core/modeling/backing-field
ms.openlocfilehash: e95417b3368d09a64f9ec02ae40c38dc770c2e6b
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="backing-fields"></a><span data-ttu-id="8a780-102">バッキング フィールド</span><span class="sxs-lookup"><span data-stu-id="8a780-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="8a780-103">この機能は、EF コア 1.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="8a780-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="8a780-104">バッキング フィールドには、読み取りまたは書き込みのプロパティではなく、フィールドを行う EF ができるようにします。</span><span class="sxs-lookup"><span data-stu-id="8a780-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="8a780-105">これを利用するクラスにカプセル化は、アプリケーション コードでの使用を制限したり、データへのアクセスの周囲のセマンティクスを強化する使用されているが、値の読み取りまたはその制限を使用せずに、データベースに書き込まれる必要があります/拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="8a780-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="8a780-106">規則</span><span class="sxs-lookup"><span data-stu-id="8a780-106">Conventions</span></span>

<span data-ttu-id="8a780-107">慣例により、バックアップする (優先順位順に表示されます)、指定したプロパティ フィールドとして、次のフィールドが検出されます。</span><span class="sxs-lookup"><span data-stu-id="8a780-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="8a780-108">フィールドは、モデルに含まれているプロパティに対してのみ検出されます。</span><span class="sxs-lookup"><span data-stu-id="8a780-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="8a780-109">詳細については、モデルに含まれるプロパティを次を参照してください。[などとプロパティを除外](included-properties.md)です。</span><span class="sxs-lookup"><span data-stu-id="8a780-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

<span data-ttu-id="8a780-110">バッキング フィールドが構成されている場合、EF は、(プロパティ set アクセス操作子を使用するのではなく) データベースからのエンティティのインスタンスを具体化する場合にそのフィールドに直接書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8a780-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="8a780-111">EF を他のタイミングで値を読み取ったり書き込んだりする場合は、可能であれば、プロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a780-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="8a780-112">たとえば、EF プロパティの値を更新する場合は、使用されますプロパティ set アクセス操作子が定義されている場合。</span><span class="sxs-lookup"><span data-stu-id="8a780-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="8a780-113">プロパティが読み取り専用である場合は、フィールドに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8a780-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8a780-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8a780-114">Data Annotations</span></span>

<span data-ttu-id="8a780-115">データ注釈を使用したフィールドをバックアップを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="8a780-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="8a780-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8a780-116">Fluent API</span></span>

<span data-ttu-id="8a780-117">Fluent API を使用して、プロパティのバッキング フィールドを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8a780-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="8a780-118">フィールドを使用する場合を制御します。</span><span class="sxs-lookup"><span data-stu-id="8a780-118">Controlling when the field is used</span></span>

<span data-ttu-id="8a780-119">EF フィールドまたはプロパティを使用する場合を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8a780-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="8a780-120">参照してください、 [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)用にサポートされるオプションです。</span><span class="sxs-lookup"><span data-stu-id="8a780-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="8a780-121">プロパティ フィールド</span><span class="sxs-lookup"><span data-stu-id="8a780-121">Fields without a property</span></span>

<span data-ttu-id="8a780-122">エンティティ クラスに対応する CLR プロパティはありませんが、代わりに、エンティティにデータを格納するフィールドを使用するモデルの概念プロパティを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8a780-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="8a780-123">これとは異なる[プロパティをシャドウ](shadow-properties.md)、変更トラッカーのデータを格納する場所です。</span><span class="sxs-lookup"><span data-stu-id="8a780-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="8a780-124">これは通常される使用、そのエンティティ クラスでは、メソッドを使用して、値を取得または設定する場合。</span><span class="sxs-lookup"><span data-stu-id="8a780-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="8a780-125">EF 内のフィールドの名前を指定する、 `Property(...)` API です。</span><span class="sxs-lookup"><span data-stu-id="8a780-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="8a780-126">場合は、指定した名前のプロパティがない、EF はフィールドのなります。</span><span class="sxs-lookup"><span data-stu-id="8a780-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="8a780-127">プロパティ、フィールド名以外の名前を入力することもできます。</span><span class="sxs-lookup"><span data-stu-id="8a780-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="8a780-128">この名前は、モデルを作成するときに使用し、データベース内にマップされている列の名前を使用する最も顕著なです。</span><span class="sxs-lookup"><span data-stu-id="8a780-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="8a780-129">エンティティ クラスでプロパティがない場合は、使用できます、`EF.Property(...)`概念的には、モデルの一部であるプロパティを参照する LINQ クエリ内のメソッドです。</span><span class="sxs-lookup"><span data-stu-id="8a780-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

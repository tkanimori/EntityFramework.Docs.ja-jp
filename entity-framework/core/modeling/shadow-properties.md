---
title: シャドウとインデクサーのプロパティ-EF Core
description: Entity Framework Core モデルでの shadow および indexer プロパティの構成
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063895"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="1a10f-103">Shadow および Indexer プロパティ</span><span class="sxs-lookup"><span data-stu-id="1a10f-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="1a10f-104">Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。</span><span class="sxs-lookup"><span data-stu-id="1a10f-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="1a10f-105">これらのプロパティの値と状態は、単に変更トラッカーに保持されます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="1a10f-106">シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1a10f-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="1a10f-107">外部キーのシャドウのプロパティ</span><span class="sxs-lookup"><span data-stu-id="1a10f-107">Foreign key shadow properties</span></span>

<span data-ttu-id="1a10f-108">シャドウプロパティは、外部キープロパティで最もよく使用されます。この場合、2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理されます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="1a10f-109">慣例により、EF はリレーションシップが検出されたときに、外部キープロパティが依存エンティティクラスに見つからない場合に shadow プロパティを導入します。</span><span class="sxs-lookup"><span data-stu-id="1a10f-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="1a10f-110">このプロパティにはという名前が付けられ `<navigation property name><principal key property name>` ます (これは、プリンシパルエンティティを指し示す依存エンティティのナビゲーションで、名前付けに使用されます)。</span><span class="sxs-lookup"><span data-stu-id="1a10f-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="1a10f-111">プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前はだけになり `<principal key property name>` ます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="1a10f-112">依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="1a10f-113">たとえば、次のコードリストでは、 `BlogId` shadow プロパティがエンティティに導入され `Post` ます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="1a10f-114">シャドウプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="1a10f-114">Configuring shadow properties</span></span>

<span data-ttu-id="1a10f-115">Fluent API を使用してシャドウプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="1a10f-116">の文字列オーバーロードを呼び出した後は、 `Property` 他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="1a10f-117">次の例では、 `Blog` にという名前の CLR プロパティがないため、 `LastUpdated` shadow プロパティが作成されています。</span><span class="sxs-lookup"><span data-stu-id="1a10f-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="1a10f-118">メソッドに指定された名前が `Property` 既存のプロパティの名前 (shadow プロパティまたは entity クラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="1a10f-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="1a10f-119">シャドウプロパティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="1a10f-119">Accessing shadow properties</span></span>

<span data-ttu-id="1a10f-120">シャドウプロパティの値は、API を使用して取得および変更でき `ChangeTracker` ます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="1a10f-121">シャドウプロパティは、静的メソッドを使用して LINQ クエリで参照でき `EF.Property` ます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="1a10f-122">返されるエンティティは変更トラッカーによって追跡されないため、no tracking クエリの後にシャドウプロパティにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1a10f-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="1a10f-123">プロパティバッグのエンティティ型</span><span class="sxs-lookup"><span data-stu-id="1a10f-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="1a10f-124">プロパティバッグエンティティ型のサポートは EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="1a10f-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="1a10f-125">インデクサープロパティのみを含むエンティティ型は、プロパティバッグエンティティ型と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="1a10f-126">これらのエンティティ型には、シャドウプロパティはありません。</span><span class="sxs-lookup"><span data-stu-id="1a10f-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="1a10f-127">現時点 `Dictionary<string, object>` では、プロパティバッグエンティティ型としてのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1a10f-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="1a10f-128">これは、一意の名前を持つ共有エンティティ型として構成する必要があり、対応する `DbSet` プロパティは呼び出しを使用して実装する必要があることを意味し `Set` ます。</span><span class="sxs-lookup"><span data-stu-id="1a10f-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
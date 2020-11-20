---
title: シャドウとインデクサーのプロパティ-EF Core
description: Entity Framework Core モデルでの shadow および indexer プロパティの構成
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: d419de2da2a9fc29e675dde76e824217347d2e9c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003433"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="180ff-103">Shadow および Indexer プロパティ</span><span class="sxs-lookup"><span data-stu-id="180ff-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="180ff-104">Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。</span><span class="sxs-lookup"><span data-stu-id="180ff-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="180ff-105">これらのプロパティの値と状態は、単に変更トラッカーに保持されます。</span><span class="sxs-lookup"><span data-stu-id="180ff-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="180ff-106">シャドウプロパティは、マップされたエンティティ型では公開されないデータがデータベースに存在する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="180ff-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="180ff-107">インデクサーのプロパティはエンティティ型のプロパティであり、.NET エンティティクラスの [インデクサー](/dotnet/csharp/programming-guide/indexers/) によってサポートされています。</span><span class="sxs-lookup"><span data-stu-id="180ff-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="180ff-108">これらは、.NET クラスインスタンスのインデクサーを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="180ff-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="180ff-109">また、CLR クラスを変更せずに、エンティティ型に追加のプロパティを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="180ff-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="180ff-110">外部キーのシャドウのプロパティ</span><span class="sxs-lookup"><span data-stu-id="180ff-110">Foreign key shadow properties</span></span>

<span data-ttu-id="180ff-111">シャドウプロパティは、外部キープロパティで最もよく使用されます。この場合、2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理されます。</span><span class="sxs-lookup"><span data-stu-id="180ff-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="180ff-112">慣例により、EF はリレーションシップが検出されたときに、外部キープロパティが依存エンティティクラスに見つからない場合に shadow プロパティを導入します。</span><span class="sxs-lookup"><span data-stu-id="180ff-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="180ff-113">このプロパティにはという名前が付けられ `<navigation property name><principal key property name>` ます (これは、プリンシパルエンティティを指し示す依存エンティティのナビゲーションで、名前付けに使用されます)。</span><span class="sxs-lookup"><span data-stu-id="180ff-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="180ff-114">プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前はだけになり `<principal key property name>` ます。</span><span class="sxs-lookup"><span data-stu-id="180ff-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="180ff-115">依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="180ff-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="180ff-116">たとえば、次のコードリストでは、 `BlogId` shadow プロパティがエンティティに導入され `Post` ます。</span><span class="sxs-lookup"><span data-stu-id="180ff-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="180ff-117">シャドウプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="180ff-117">Configuring shadow properties</span></span>

<span data-ttu-id="180ff-118">Fluent API を使用してシャドウプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="180ff-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="180ff-119">の文字列オーバーロードを呼び出した後は、 `Property` 他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。</span><span class="sxs-lookup"><span data-stu-id="180ff-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="180ff-120">次の例では、 `Blog` にという名前の CLR プロパティがないため、 `LastUpdated` shadow プロパティが作成されています。</span><span class="sxs-lookup"><span data-stu-id="180ff-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="180ff-121">メソッドに指定された名前が `Property` 既存のプロパティの名前 (shadow プロパティまたは entity クラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="180ff-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="180ff-122">シャドウプロパティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="180ff-122">Accessing shadow properties</span></span>

<span data-ttu-id="180ff-123">シャドウプロパティの値は、API を使用して取得および変更でき `ChangeTracker` ます。</span><span class="sxs-lookup"><span data-stu-id="180ff-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="180ff-124">シャドウプロパティは、静的メソッドを使用して LINQ クエリで参照でき `EF.Property` ます。</span><span class="sxs-lookup"><span data-stu-id="180ff-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="180ff-125">返されるエンティティは変更トラッカーによって追跡されないため、no tracking クエリの後にシャドウプロパティにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="180ff-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="180ff-126">インデクサーのプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="180ff-126">Configuring indexer properties</span></span>

<span data-ttu-id="180ff-127">Fluent API を使用して、インデクサーのプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="180ff-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="180ff-128">メソッドを呼び出した後は、 `IndexerProperty` 他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。</span><span class="sxs-lookup"><span data-stu-id="180ff-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="180ff-129">次の例では、にインデクサーが定義されて `Blog` おり、インデクサープロパティを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="180ff-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="180ff-130">メソッドに指定した名前が `IndexerProperty` 既存のインデクサープロパティの名前と一致する場合、コードによって既存のプロパティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="180ff-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="180ff-131">エンティティ型に、エンティティクラスのプロパティによってサポートされるプロパティがある場合、インデクサープロパティにはインデクサー経由でのみアクセスする必要があるため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="180ff-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="180ff-132">プロパティバッグのエンティティ型</span><span class="sxs-lookup"><span data-stu-id="180ff-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="180ff-133">プロパティバッグエンティティ型のサポートは EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="180ff-133">Support for Property bag entity types was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="180ff-134">インデクサープロパティのみを含むエンティティ型は、プロパティバッグエンティティ型と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="180ff-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="180ff-135">これらのエンティティ型にはシャドウプロパティがないため、EF はインデクサープロパティを作成します。</span><span class="sxs-lookup"><span data-stu-id="180ff-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="180ff-136">現時点 `Dictionary<string, object>` では、プロパティバッグエンティティ型としてのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="180ff-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="180ff-137">これは、一意の名前を持つ共有エンティティ型として構成する必要があり、対応する `DbSet` プロパティは呼び出しを使用して実装する必要があり `Set` ます。</span><span class="sxs-lookup"><span data-stu-id="180ff-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]

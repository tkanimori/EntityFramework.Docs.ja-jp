---
title: シャドウのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: ab57358dd247e32c4ca0f57d07b4cb98f2b85d29
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655963"
---
# <a name="shadow-properties"></a><span data-ttu-id="c2658-102">シャドウ プロパティ</span><span class="sxs-lookup"><span data-stu-id="c2658-102">Shadow Properties</span></span>

<span data-ttu-id="c2658-103">Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。</span><span class="sxs-lookup"><span data-stu-id="c2658-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="c2658-104">これらのプロパティの値と状態は、単に変更トラッカーに保持されます。</span><span class="sxs-lookup"><span data-stu-id="c2658-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="c2658-105">シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c2658-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="c2658-106">2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理される外部キープロパティで最もよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="c2658-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="c2658-107">シャドウプロパティの値は、`ChangeTracker` API を使用して取得および変更できます。</span><span class="sxs-lookup"><span data-stu-id="c2658-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="c2658-108">シャドウプロパティは、`EF.Property` の静的メソッドを使用して LINQ クエリで参照できます。</span><span class="sxs-lookup"><span data-stu-id="c2658-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="c2658-109">規約</span><span class="sxs-lookup"><span data-stu-id="c2658-109">Conventions</span></span>

<span data-ttu-id="c2658-110">リレーションシップが検出されたが、依存エンティティクラスで外部キープロパティが見つからない場合は、規則によってシャドウプロパティを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c2658-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="c2658-111">この場合、shadow 外部キープロパティが導入されます。</span><span class="sxs-lookup"><span data-stu-id="c2658-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="c2658-112">Shadow 外部キーのプロパティには `<navigation property name><principal key property name>` という名前が付けられます (プリンシパルエンティティを指す依存エンティティのナビゲーションは、名前付けに使用されます)。</span><span class="sxs-lookup"><span data-stu-id="c2658-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="c2658-113">プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前は `<principal key property name>`のみになります。</span><span class="sxs-lookup"><span data-stu-id="c2658-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="c2658-114">依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c2658-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="c2658-115">たとえば、次のコードリストでは、`Post` エンティティに `BlogId` shadow プロパティが導入されます。</span><span class="sxs-lookup"><span data-stu-id="c2658-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions)]

## <a name="data-annotations"></a><span data-ttu-id="c2658-116">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c2658-116">Data Annotations</span></span>

<span data-ttu-id="c2658-117">シャドウプロパティをデータ注釈と共に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c2658-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c2658-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c2658-118">Fluent API</span></span>

<span data-ttu-id="c2658-119">Fluent API を使用してシャドウプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c2658-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="c2658-120">`Property` の文字列オーバーロードを呼び出した後は、他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。</span><span class="sxs-lookup"><span data-stu-id="c2658-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="c2658-121">`Property` メソッドに指定された名前が既存のプロパティの名前 (shadow プロパティまたはエンティティクラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="c2658-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

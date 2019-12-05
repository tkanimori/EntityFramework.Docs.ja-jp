---
title: 継承-EF Core
description: Entity Framework Core を使用してエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 4d43a432174c92ab7f3f9d78a234aefb0a4a17e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824680"
---
# <a name="inheritance"></a><span data-ttu-id="106ab-103">継承</span><span class="sxs-lookup"><span data-stu-id="106ab-103">Inheritance</span></span>

<span data-ttu-id="106ab-104">EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="106ab-104">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="106ab-105">規約</span><span class="sxs-lookup"><span data-stu-id="106ab-105">Conventions</span></span>

<span data-ttu-id="106ab-106">既定では、データベースでの継承の表現方法は、データベースプロバイダーによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="106ab-106">By default, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="106ab-107">リレーショナルデータベースプロバイダーでの処理方法については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="106ab-107">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="106ab-108">EF では、2つ以上の継承型がモデルに明示的に含まれている場合にのみ継承が設定されます。</span><span class="sxs-lookup"><span data-stu-id="106ab-108">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="106ab-109">EF では、モデルに含まれていない基本型または派生型はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="106ab-109">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="106ab-110">継承階層の各型に対して*Dbset\<TEntity >* を公開することにより、モデルに型を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="106ab-110">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="106ab-111">階層内の1つ以上のエンティティに対して*Dbset\<TEntity >* を公開しない場合は、Fluent API を使用してモデルに含まれていることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="106ab-111">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="106ab-112">また、規則に依存しない場合は、`HasBaseType`を使用して基本型を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="106ab-112">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="106ab-113">`.HasBaseType((Type)null)` を使用すると、階層からエンティティ型を削除できます。</span><span class="sxs-lookup"><span data-stu-id="106ab-113">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="106ab-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="106ab-114">Data Annotations</span></span>

<span data-ttu-id="106ab-115">データ注釈を使用して継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="106ab-115">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="106ab-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="106ab-116">Fluent API</span></span>

<span data-ttu-id="106ab-117">継承用の Fluent API は、使用しているデータベースプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="106ab-117">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="106ab-118">リレーショナルデータベースプロバイダーに対して実行できる構成については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="106ab-118">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

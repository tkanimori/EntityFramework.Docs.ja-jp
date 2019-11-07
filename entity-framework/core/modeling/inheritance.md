---
title: 継承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: abc1caa4d3839b7cdb52b316bcfc8f648b609b70
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655686"
---
# <a name="inheritance"></a><span data-ttu-id="7674b-102">継承</span><span class="sxs-lookup"><span data-stu-id="7674b-102">Inheritance</span></span>

<span data-ttu-id="7674b-103">EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7674b-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="7674b-104">規約</span><span class="sxs-lookup"><span data-stu-id="7674b-104">Conventions</span></span>

<span data-ttu-id="7674b-105">慣例により、データベースでの継承の表現方法は、データベースプロバイダーによって決まります。</span><span class="sxs-lookup"><span data-stu-id="7674b-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="7674b-106">リレーショナルデータベースプロバイダーでの処理方法については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7674b-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="7674b-107">EF では、2つ以上の継承型がモデルに明示的に含まれている場合にのみ継承が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7674b-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="7674b-108">EF では、モデルに含まれていない基本型または派生型はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="7674b-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="7674b-109">継承階層の各型に対して*Dbset\<TEntity >* を公開することにより、モデルに型を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="7674b-109">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="7674b-110">階層内の1つ以上のエンティティに対して*Dbset\<TEntity >* を公開しない場合は、Fluent API を使用してモデルに含まれていることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="7674b-110">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="7674b-111">また、規則に依存しない場合は、`HasBaseType`を使用して基本型を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="7674b-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="7674b-112">`.HasBaseType((Type)null)` を使用すると、階層からエンティティ型を削除できます。</span><span class="sxs-lookup"><span data-stu-id="7674b-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7674b-113">データの注釈</span><span class="sxs-lookup"><span data-stu-id="7674b-113">Data Annotations</span></span>

<span data-ttu-id="7674b-114">データ注釈を使用して継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="7674b-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="7674b-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7674b-115">Fluent API</span></span>

<span data-ttu-id="7674b-116">継承用の Fluent API は、使用しているデータベースプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="7674b-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="7674b-117">リレーショナルデータベースプロバイダーに対して実行できる構成については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7674b-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

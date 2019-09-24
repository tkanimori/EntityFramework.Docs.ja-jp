---
title: 継承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: 1f20c455176b5922364584f8c7688c15a4c3f0f9
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197286"
---
# <a name="inheritance"></a><span data-ttu-id="069ac-102">継承</span><span class="sxs-lookup"><span data-stu-id="069ac-102">Inheritance</span></span>

<span data-ttu-id="069ac-103">EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="069ac-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="069ac-104">規約</span><span class="sxs-lookup"><span data-stu-id="069ac-104">Conventions</span></span>

<span data-ttu-id="069ac-105">慣例により、データベースでの継承の表現方法は、データベースプロバイダーによって決まります。</span><span class="sxs-lookup"><span data-stu-id="069ac-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="069ac-106">リレーショナルデータベースプロバイダーでの処理方法については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="069ac-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="069ac-107">EF では、2つ以上の継承型がモデルに明示的に含まれている場合にのみ継承が設定されます。</span><span class="sxs-lookup"><span data-stu-id="069ac-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="069ac-108">EF では、モデルに含まれていない基本型または派生型はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="069ac-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="069ac-109">継承階層内の型ごとに*dbset<TEntity>* を公開することにより、モデルに型を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="069ac-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="069ac-110">階層内の1つ以上のエンティティに対して*dbset<TEntity>* を公開しない場合は、Fluent API を使用してモデルに含まれるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="069ac-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="069ac-111">また、規則に依存しない場合は、を使用して`HasBaseType`基本型を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="069ac-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="069ac-112">を使用`.HasBaseType((Type)null)`すると、階層からエンティティ型を削除できます。</span><span class="sxs-lookup"><span data-stu-id="069ac-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="069ac-113">データの注釈</span><span class="sxs-lookup"><span data-stu-id="069ac-113">Data Annotations</span></span>

<span data-ttu-id="069ac-114">データ注釈を使用して継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="069ac-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="069ac-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="069ac-115">Fluent API</span></span>

<span data-ttu-id="069ac-116">継承用の Fluent API は、使用しているデータベースプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="069ac-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="069ac-117">リレーショナルデータベースプロバイダーに対して実行できる構成については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="069ac-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

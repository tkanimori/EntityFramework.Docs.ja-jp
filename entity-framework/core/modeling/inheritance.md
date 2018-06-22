---
title: 継承の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
ms.technology: entity-framework-core
uid: core/modeling/inheritance
ms.openlocfilehash: f0394bc55dfbfea8277b1ddf898361165dd1fe51
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052782"
---
# <a name="inheritance"></a><span data-ttu-id="6eb1f-102">継承</span><span class="sxs-lookup"><span data-stu-id="6eb1f-102">Inheritance</span></span>

<span data-ttu-id="6eb1f-103">EF モデルでの継承を使用すると、データベース内のエンティティ クラスの継承の表現方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="6eb1f-104">規則</span><span class="sxs-lookup"><span data-stu-id="6eb1f-104">Conventions</span></span>

<span data-ttu-id="6eb1f-105">慣例により、データベースでの継承の表現方法を決定する、データベース プロバイダーの責任です。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="6eb1f-106">参照してください[(リレーショナル データベース) の継承](relational/inheritance.md)のリレーショナル データベース プロバイダーで処理する方法です。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="6eb1f-107">モデルで、2 つまたは複数の継承された型が明示的に含まれている場合、EF は継承設定のみです。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="6eb1f-108">EF は基本データ型または派生型それ以外の場合、モデルに含まれていないことをスキャンしません。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="6eb1f-109">公開することで、モデル内の型を含めることができます、 *DbSet<TEntity>* 継承階層の種類ごとにします。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="6eb1f-110">公開するたくない場合、 *DbSet<TEntity>* 階層内の 1 つまたは複数のエンティティ、モデルに含まれていることを確認する Fluent API を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="6eb1f-111">規則に依存しないようにする場合は、明示的に使用して、基本型を指定できますと`HasBaseType`です。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-111">And if you don't rely on conventions you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="6eb1f-112">使用することができます`.HasBaseType((Type)null)`階層からエンティティの種類を削除します。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6eb1f-113">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6eb1f-113">Data Annotations</span></span>

<span data-ttu-id="6eb1f-114">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6eb1f-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6eb1f-115">Fluent API</span></span>

<span data-ttu-id="6eb1f-116">継承の Fluent API を使用しているデータベース プロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="6eb1f-117">参照してください[(リレーショナル データベース) の継承](relational/inheritance.md)のリレーショナル データベース プロバイダーに対して実行できる構成します。</span><span class="sxs-lookup"><span data-stu-id="6eb1f-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

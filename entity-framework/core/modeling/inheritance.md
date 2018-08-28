---
title: 継承 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: c5fa9d13dec8cfc3e1cac69e471f509cbbb9e4c5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995897"
---
# <a name="inheritance"></a><span data-ttu-id="4bb8a-102">継承</span><span class="sxs-lookup"><span data-stu-id="4bb8a-102">Inheritance</span></span>

<span data-ttu-id="4bb8a-103">EF モデルでの継承は、データベース内でエンティティ クラスの継承を表現する方法を制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="4bb8a-104">規約</span><span class="sxs-lookup"><span data-stu-id="4bb8a-104">Conventions</span></span>

<span data-ttu-id="4bb8a-105">慣例により、データベース内の継承の表示方法を指定するデータベース プロバイダーの責任です。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="4bb8a-106">参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)のリレーショナル データベース プロバイダーで処理する方法。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="4bb8a-107">2 つまたは複数の継承された型がモデルで明示的に含まれている場合、EF は継承設定のみ。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="4bb8a-108">それ以外の場合、モデルに含まれていたいない基本または派生型の場合は、EF をスキャンしません。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="4bb8a-109">公開することで、モデルの型を含めることができます、 *DbSet<TEntity>* 継承階層の種類ごとにします。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="4bb8a-110">公開するたくない場合、 *DbSet<TEntity>* 階層の 1 つまたは複数のエンティティに対して、モデルに含まれていることを確認する、Fluent API を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="4bb8a-111">規則に依存しないようにする場合は、明示的に使用する基本型を指定できますと`HasBaseType`します。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-111">And if you don't rely on conventions you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="4bb8a-112">使用することができます`.HasBaseType((Type)null)`エンティティ型を階層から削除します。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4bb8a-113">データの注釈</span><span class="sxs-lookup"><span data-stu-id="4bb8a-113">Data Annotations</span></span>

<span data-ttu-id="4bb8a-114">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="4bb8a-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="4bb8a-115">Fluent API</span></span>

<span data-ttu-id="4bb8a-116">継承の Fluent API を使用するデータベース プロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="4bb8a-117">参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)の構成のリレーショナル データベース プロバイダーを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4bb8a-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

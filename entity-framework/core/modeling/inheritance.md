---
title: 継承 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: f6b5c8f5a398ac1e28e29bc17f0674c5b76d7b20
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319128"
---
# <a name="inheritance"></a><span data-ttu-id="ac979-102">継承</span><span class="sxs-lookup"><span data-stu-id="ac979-102">Inheritance</span></span>

<span data-ttu-id="ac979-103">EF モデルでの継承は、データベース内でエンティティ クラスの継承を表現する方法を制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac979-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="ac979-104">規約</span><span class="sxs-lookup"><span data-stu-id="ac979-104">Conventions</span></span>

<span data-ttu-id="ac979-105">慣例により、データベース内の継承の表示方法を指定するデータベース プロバイダーの責任です。</span><span class="sxs-lookup"><span data-stu-id="ac979-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="ac979-106">参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)のリレーショナル データベース プロバイダーで処理する方法。</span><span class="sxs-lookup"><span data-stu-id="ac979-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="ac979-107">2 つまたは複数の継承された型がモデルで明示的に含まれている場合、EF は継承設定のみ。</span><span class="sxs-lookup"><span data-stu-id="ac979-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="ac979-108">それ以外の場合、モデルに含まれていたいない基本または派生型の場合は、EF をスキャンしません。</span><span class="sxs-lookup"><span data-stu-id="ac979-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="ac979-109">公開することで、モデルの型を含めることができます、 *DbSet<TEntity>* 継承階層の種類ごとにします。</span><span class="sxs-lookup"><span data-stu-id="ac979-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="ac979-110">公開するたくない場合、 *DbSet<TEntity>* 階層の 1 つまたは複数のエンティティに対して、モデルに含まれていることを確認する、Fluent API を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ac979-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="ac979-111">規則に依存しない場合は、明示的に使用する基本型を指定できますと`HasBaseType`します。</span><span class="sxs-lookup"><span data-stu-id="ac979-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="ac979-112">使用することができます`.HasBaseType((Type)null)`エンティティ型を階層から削除します。</span><span class="sxs-lookup"><span data-stu-id="ac979-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ac979-113">データの注釈</span><span class="sxs-lookup"><span data-stu-id="ac979-113">Data Annotations</span></span>

<span data-ttu-id="ac979-114">データ注釈を使用して、継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="ac979-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ac979-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ac979-115">Fluent API</span></span>

<span data-ttu-id="ac979-116">継承の Fluent API を使用するデータベース プロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ac979-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="ac979-117">参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)の構成のリレーショナル データベース プロバイダーを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ac979-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>

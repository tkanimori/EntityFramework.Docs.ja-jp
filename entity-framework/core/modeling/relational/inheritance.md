---
title: 継承 (リレーショナルデータベース)-EF Core
description: Entity Framework Core を使用してリレーショナルデータベースでエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 30e25aa2968ceab03404baddb46e0ae59fc3ea6b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824746"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="c9f58-103">継承 (リレーショナル データベース)</span><span class="sxs-lookup"><span data-stu-id="c9f58-103">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="c9f58-104">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-104">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c9f58-105">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c9f58-105">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c9f58-106">EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-106">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="c9f58-107">現時点では、EF Core で実装されているのは、階層単位 (TPH) パターンだけです。</span><span class="sxs-lookup"><span data-stu-id="c9f58-107">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="c9f58-108">テーブルごとのテーブル (TPT) や具象型ごとのテーブル (TPC) などのその他の一般的なパターンは、まだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="c9f58-108">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="c9f58-109">規約</span><span class="sxs-lookup"><span data-stu-id="c9f58-109">Conventions</span></span>

<span data-ttu-id="c9f58-110">既定では、継承は、階層単位 (TPH) パターンを使用してマップされます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-110">By default, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="c9f58-111">TPH は、1つのテーブルを使用して、階層内のすべての型のデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="c9f58-111">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="c9f58-112">識別子列は、各行が表す型を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-112">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="c9f58-113">EF Core は、継承された複数の型がモデルに明示的に含まれている場合にのみ継承を設定します (詳細については、「[継承](../inheritance.md)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="c9f58-113">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="c9f58-114">単純な継承シナリオと、TPH パターンを使用してリレーショナルデータベーステーブルに格納されているデータを示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c9f58-114">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="c9f58-115">*識別子*列には、各行に格納されている*ブログ*の種類が示されます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-115">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![イメージ](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="c9f58-117">TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。</span><span class="sxs-lookup"><span data-stu-id="c9f58-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c9f58-118">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c9f58-118">Data Annotations</span></span>

<span data-ttu-id="c9f58-119">データ注釈を使用して継承を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c9f58-119">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c9f58-120">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c9f58-120">Fluent API</span></span>

<span data-ttu-id="c9f58-121">Fluent API を使用して、識別子列の名前と型、および階層内の各型を識別するために使用される値を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-121">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="c9f58-122">識別子プロパティの構成</span><span class="sxs-lookup"><span data-stu-id="c9f58-122">Configuring the discriminator property</span></span>

<span data-ttu-id="c9f58-123">上の例では、識別子は、階層の基本エンティティの[shadow プロパティ](xref:core/modeling/shadow-properties)として作成されます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-123">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="c9f58-124">モデルのプロパティであるため、他のプロパティと同様に構成できます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-124">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="c9f58-125">たとえば、既定の規則に従った識別子を使用する場合の最大長を設定するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c9f58-125">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/DefaultDiscriminator.cs#DiscriminatorConfiguration)]

<span data-ttu-id="c9f58-126">識別子は、エンティティ内の .NET プロパティにマップして構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-126">The discriminator can also be mapped to a .NET property in your entity and configure it.</span></span> <span data-ttu-id="c9f58-127">例:</span><span class="sxs-lookup"><span data-stu-id="c9f58-127">For example:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs#NonShadowDiscriminator)]

## <a name="shared-columns"></a><span data-ttu-id="c9f58-128">共有列</span><span class="sxs-lookup"><span data-stu-id="c9f58-128">Shared columns</span></span>

<span data-ttu-id="c9f58-129">2つの兄弟エンティティ型に同じ名前のプロパティがある場合、既定では2つの異なる列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-129">When two sibling entity types have a property with the same name they will be mapped to two separate columns by default.</span></span> <span data-ttu-id="c9f58-130">ただし、互換性がある場合は、同じ列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="c9f58-130">But if they are compatible they can be mapped to the same column:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs#SharedTPHColumns)]